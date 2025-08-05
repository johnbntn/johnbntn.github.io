+++
title = 'Hacking a Pass Together in LLVM`s OCaml Bindings'
date = 2025-08-03T09:47:53-04:00
draft = false
+++

If you're writing a production quality compiler, it's no longer good enough to just have correct code, you need speed. Compiler optimizations have become an incredibly important aspect of modern compiler design and will only grow in importance as languages are developed for increasingly specialized, computation heavy domains.

## What is LLVM?

LLVM (which [doesn't stand for anything](https://lists.llvm.org/pipermail/llvm-dev/2011-December/046445.html)) is a codegen and optimization engine. It stands out for being architecture independent, so you can write optimizations in LLVM's IR and let LLVM handle generating code for specific architectures.

However, it's incredibly difficult to write optimized code all at once. LLVM handles this with *passes*, which are loops over the IR, modifying it in place and replacing blocks of code with faster blocks of code.

The main LLVM bindings are written in C++ but OCaml bindings also exist. However, as far as I know, there aren't any popular compilers using the OCaml bindings and as a result, they aren't maintained as rigourously and there are some features in the C++ bindings that don't exist in the OCaml ones. Thankfully, most of the codegen API exists but the pass manager API is almost non-existent.

Thankfully, with a little effort, we can work around this.

All the code in this post is just OCaml pseudo-code, the full project can be found [here](https://github.com/johnbntn/bfvm). Specifically, the `lib/passes.ml` file.

This post also expects some familiarity with LLVM, though not necessarily implementing your own passes. I found the [Kaleidoscope Tutorial](https://releases.llvm.org/12.0.0/docs/tutorial/index.html) to be helpful 

## Iterables

The first thing you'll have to do is expose the `modules` you wish to optimize. OCaml makes this relatively straightforward: if you have a codegen function that returns `unit`, modify it to return your module instead.
```
let _ = List.iter (codegen_op ~env) tokens in
the_module
```
Or, if you need your codegen function to return something else, return a tuple.
```
let ret = codegen_op ~env tokens in
(ret, the_module)
```

We need the module to manually hook into our generated code using the `Llvm.iter_functions` and `Llvm.iter_blocks` functions.

```
let optimize the_module =
  Llvm.iter_functions
    (fun fn ->
      Llvm.iter_blocks
        (fun bb ->
          let first_instr =
            match Llvm.instr_begin bb with
            | At_end _ -> None
            | Before instr -> Some instr
          in
          let _ = pass_state_machine the_module first_instr Looking bb in
          ())
        fn)
    the_module
```
We use OCaml's wonderful anonymous functions in to iterate through every basic block of every function, get the first instruction of each basic block, and start trying to optimize.

## Folding Loops

It's here that this post will become somewhat language specific; to give a concrete and also very simple example, I'll be using the [Brainf*ck](https://en.wikipedia.org/wiki/Brainfuck) programming language. 

BF is an incredibly small (8 instructions) programming language that models a turing machine. There's a tape containing cells and a pointer to your current cell. You can increment/decrement the pointer by one, increment/decrement the cell value at the pointer by one, take in a byte as input, output the current cell value, or loop. Loops in BF start at a left-bracket `[`, if the current cell value is zero, jump to after the corresponding right-bracket `]`. Otherwise, it continues executing to the corresponding right bracket, where it again compares the current cell value and jumps back if it's non-zero.

In BF, setting a cell to 0 can only be done using a loop:

```text
[-]
```
Where this sequence of instructions tells the program to keep decrementing the current cell value until it's 0. This takes $O(n)$ where $n$ is your current cell value, which is ridiculous. We want to replace this sequence of instructions with a simple load and store. 

When a program containing this pattern is generated the relevant LLVM IR looks like this:
```text
...
loop_after_0:                                     ; preds = %loop_header_0
  ...

loop_body_0:                                      ; preds = %loop_header_0
  %load_ptr12 = load i32, ptr @ptr, align 4
  %tape_cell_addr13 = getelementptr [30000 x i8], ptr @tape, i32 0, i32 %load_ptr12
  %tape_cell_val14 = load i8, ptr %tape_cell_addr13, align 1
  %sub_from_cell = sub i8 %tape_cell_val14, 1
  store i8 %sub_from_cell, ptr %tape_cell_addr13, align 1
  br label %loop_header_0

loop_header_0:                                    ; preds = %loop_body_0, %entry
  %load_ptr9 = load i32, ptr @ptr, align 4
  %tape_cell_addr10 = getelementptr [30000 x i8], ptr @tape, i32 0, i32 %load_ptr9
  %tape_cell_val11 = load i8, ptr %tape_cell_addr10, align 1
  %cmp = icmp ne i8 %tape_cell_val11, 0
  br i1 %cmp, label %loop_body_0, label %loop_after_0
...
```

We can see that the `loop_body_0` block fist loads the correct cell, subtracts one from it and jumps to `loop_header_0`. The loop header *again* loads the correct cell and compares it to 0 before jumping to the body or after block.

Instead, we want the loop header to load the correct cell, set it to zero, then unconditionally jump to the after block.

```text
...
loop_after_0:                                     ; preds = %loop_header_0
  ...

loop_header_0:                                    ; preds = %entry
  %load_ptr9 = load i32, ptr @ptr, align 4
  %tape_cell_addr10 = getelementptr [30000 x i8], ptr @tape, i32 0, i32 %load_ptr9
  %tape_cell_val11 = load i8, ptr %tape_cell_addr10, align 1
  store i8 0, i8 %tape_cell_val11, align 1
  br label %loop_after_0
...
```

## State Machines

By recognizing patterns in the IR, we know when to start optimizing. In the above example, we know that when a basic block consists entirely of the load, gep, load, sub, store, and br instructions, we can remove it. To find patterns in the IR, we can make a state machine. Every state represents getting a step closer to the pattern you need to start optimizing. OCaml makes this very easy with support for ADTs.

```
type fold_loop_state =
  | Looking
  | FoundLoadPtr
  | FoundGep
  | FoundLoadTape
  | FoundSub
  | FoundStore
```

Using the `Llvm.instr_succ` and `Llvm.instr_opcode` functions, we can loop through every instruction in a block and start folding the loop away when we match the optimization pattern.

```
let rec fold_loop_state_machine the_module curr_instr state bb =
...
 let op = Llvm.instr_opcode instr in
      let next_instr = get_next instr in
      let open Llvm.Opcode in
      match op with
      | Load -> (
          match state with
          | Looking ->
              fold_loop_state_machine the_module next_instr FoundLoadPtr bb
          | FoundGep ->
              fold_loop_state_machine the_module next_instr FoundLoadTape bb
          | _ -> ())
...
      | Br -> ( match state with FoundStore -> fold the_module bb | _ -> ())
      | _ -> ()
```

## Optimizing

Once you've found the point in your program to optimize, you need to start inserting/removing instructions to do it. This can be accomplished with the `Llvm.delete_instr` and `Llvm.build_*` functions. Note that to build new instructions, you'll need an IR builder which can be created with the module.
```
let the_context = Llvm.module_context the_module in
let builder = Llvm.builder the_context in
```
If you need to modify the IR in another basic block, you can use the `Llvm.block_pred` or `Llvm.block_succ` functions. 

I also found that Llvm wouldn't let me delete a basic block. I believe this to be a bug because earlier versions of the bindings had support for the `add_cfg_simplification` pass which performed similar optimizations to the one described above. However, this was removed with an update to LLVM, leading me to believe that they had similar difficulty in pruning basic blocks.

However, you can still clear every instruction and mark it as unreachable.

```
let clear_block block =
  let rec clear_instructions () =
    match Llvm.instr_begin block with
    | At_end _ -> ()
    | Before instr ->
        Llvm.delete_instruction instr;
        clear_instructions ()
  in
  clear_instructions ()
in
let _ = clear_block bb in
let _ = Llvm.position_at_end bb builder in
let _ = Llvm.build_unreachable builder in
```

## Conclusion

And that's it! Hopefully you learned a bit about LLVM and OCaml. Happy Optimizing!
