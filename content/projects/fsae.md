+++
title = 'Nittany Motorsports'
date = 2025-04-05T12:52:32-04:00
draft = false
+++

## Nittany Motorsports

Here are some projects I did for Nittany Motorsports, Penn State's FSAE Team:

### LV BMS

Wrote firmware for a low voltage (20V) passive BMS system for a FSAE vehicle. This was done using an [STM32L432KC](https://www.st.com/en/microcontrollers-microprocessors/stm32l432kc.html) and a [BQ76905](https://www.ti.com/product/BQ76905?utm_source=google&utm_medium=cpc&utm_campaign=app-null-null-GPN_EN-cpc-pf-google-ww_en_cons&utm_content=BQ76905&ds_k=BQ76905&DCM=yes&gclsrc=aw.ds&gad_source=1&gad_campaignid=1767856010&gclid=EAIaIQobChMIgI75oOq_jQMVdHFHAR01xydqEAAYASAAEgKlRvD_BwE) BMS chip. The system communicates over I2C and also sends CAN to the vehicle's bus. There is support for a multitude of protections including over/under voltage, over/under temperature, short circuit in charge/discharge, and more.

### HIL (Hardware In the Loop) Testbench

Mimic running the car to test the Accelerator Pedal Position Sensor (APPS) and Ready to Drive functionality using a Raspberry Pi to send voltage and CAN. Here's a picture of our setup.

![Setup](/images/projects/fsae/hil.jpg)

