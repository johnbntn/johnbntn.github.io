+++
title = '{{ replace .File.ContentBaseName "_" " " | title }}'
date = {{ .Date }}
draft = true
slug = '{{ replace .File.ContentBaseName "_" "-" | slug | lower }}'
+++
