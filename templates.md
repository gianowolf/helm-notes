# Templates

- ```.yaml``` extension to YAML output
- ```.tpl``` for no formatted content
- ```should-use-dashed-notation.yaml```
- Each resource def should be in its own template file
  
## Defined Templates

- ```{{ define }}```
- globally accessible: A chart and all its subcharts will have access to defined templates
- all defined templates should be namespaced

```yaml
{{- define "nginx.fullname" }}
{{ /* ... */  }}
{{ end -}}
```

## Formatting Templates

- should be indented using two spaces (never tabs)
- should have whitespace after the opening braces
- should have whitespace before closing braces 
- blocks and control structures may be indented to indicate flow of the code 
- Template comments ```{{ /* ... */  }}``` should be used when documenting features 
- YAML comments ```# ...``` may be used when it is useful for Helm users to see the comments during debugging

## Named Templates

- Templates defined inside of a file 

