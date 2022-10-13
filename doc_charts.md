# Topics 

## Charts 

- Collection of files
- Describes a related set of K8s reso8urces
- Created as files laid out in a particular directory tree

## File Structure

- Collection of files in a directory 
- Directory name > Chart name

```txt
chartname/
    charts/             # contains charts dependencies 
    crds/               # Custom Resource Definitions
    templates/          # Combined with values will gen valid k8s manifest files
        NOTES.txt       # OPTIONAL usage notes 
    Chart.yaml          # Info about the chart 
    LICENSE             # OPTIONAL
    README.md           # OPTIONAL
    values.yaml         # default config values for this chart
    values.schema.json  # OPTIONAL for imposing a structure on the values
```

## Chart.yaml

```yaml
apiVersion: chart API version                           # REQUIRED
name: name of the chart                                 # REQUIRED
version: SemVer 2 version                               # REQUIRED
kubeVersion: SemVer range of comptaible K8s versions
description: single-sentence description ofv the project
type: type of the chart 
keywords: 
  - a list of keywords about the project
home: URL of the project home paje
sources:
  - a list of URLs to source code 
dependencies:
  - name: name of the dependencie chart # required
    version: version of the chart       # required
    repository: repo (URL or alias)
    condition: yaml path that resolves to a boolean (ENA/DISA charts)
    tags:
      - can be used to group charts for ena/disa together 
    import-values:
      - ImportValues 
    alias: alias used for the chart, useful when used multiple same chart 
maitainers:
  - name: maintainers name # required
    email: maintainers email
    url: URL of the maintainer
icon: URL to an SVG or PNG image to be used as an icon
appVersion: version of the apps that this contains. Use quotes
deprecated: Whether this chart is deprecated. Boolean
annotations:
    example: Listo f annotations keyed by name 
```

### version Field

- Must have a version number 
- SemVer 2 Standard 
- ```version``` field is used by many Helm tools, including the CLI
- System asumes that version number matches version number 
  - a ```nginx``` chart ```version: 1.2.3``` will be ```nginx-1.2.3.tgz```

### apiVersion Field

- v2

### appVersion Field

- informational field
- Has no impact on chart version calculations 
- warpping the version in quotes is highly recommended

### kubeVersion

- ```>= 1.13.0 < 1.15.0``` range 
- ```>= 1.13.0 < 1.14.0 || >=1.14.1 < 1.15.0``` (version 1.14.0 is excluded)
- ```=, !=, <, >, <=, =>``` supported
- hypen ranges: ```1.1 - 2.3.4``` are supported 
- wildcards: ```x, X and *``` are supported
- tilde ranges ~1.2.3 (eq >= 1.2.3 < 1.2.3)
- caret ranges ```^1.2.3``` equivalent ```>= 1.2.3 < 2.0.0```

### deprecated

- can be used to mark a chart as deprecated
- if the latest version of a chart in a repo is marked as deprecated, then the chart as a whole is considered to be deprecated

