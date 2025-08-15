+++
title = '{{ replace .File.ContentBaseName "_" " " | title }}'
date = {{ .Date }}
draft = true
slug = '{{ replace .File.ContentBaseName "_" "-" | title }}'
tags = [{{ range $plural, $terms := .Site.Taxonomies }}{{ range $term, $val := $terms }}"{{ printf "%s" $term }}",{{ end }}{{ end }}]
+++