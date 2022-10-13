<snip>
</snip>

## Chart Dependencies 

- one chart may depend on any numner of other charts 
- dynamically linked using **dependencies** field in ```Chart.yaml```

### Managing with dependencies field

```yaml
<snip>
dependencies:
  - name: apache
    version: 1.2.3
    repository: https://example.com/charts
  - name: mysql
    version: 3.2.1
    repository: https://another.example.com/charts 
```

You must also use ```helm repo add``` to add repo locally. You might use the name of the repo instead of UR:

```sh
helm repo add fantastic-charts https://fantastic-charts.sotrage.googleapis.com
```

```yaml
dependencies:
  - name: awesomeness
    version: 1.0.0
    repository: "@fantastic-charts"
```

1. Define chart dependencies
2. Run ```helm depenedency update``` to download charts into ```charts/``` directory 

### Other dependencies fields

#### alias field

- in case where need to access a chart wit other names


#### tags and condition fields

- Charts are loaded by default
  - if tags or condition fields are present they will be evaluated and used to control loading for the charts
- **condition:** will be enabled or disabled based on one or more YAML paths resolved to a boolean value
- **tags**: is a list of labels to associate with this chart. All charts with tags can be enabled or disabled by specifying the tag and a boolean value
- 
```yaml
dependencies:
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    condition: subchart1.enabled,global.subchart1.enabled
    tags:
      - front-end
      - subchart1
    alias: new-subchart-1
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    alias: new-subchart-2
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    condition: new-subchart-2.enabled,global.subchart2.enabled
    tags:
      - back-end
      - new-subchart-2
```

```yaml
# values.yaml
subchart1:
    enabled: true
tags:
    front-end: false
    back-end: true
```

or via CLI --set parameter

```sh
helm install --set tags.front-end=true --set subchart2.enabled=false
```

- tags & condition resolution
    1. Conditions override tags
       1. first condition path wins
    2. tags are evaluated as a logic OR

## Importing Child Values via dependencies 

- values to be imported are specified in ```dependencies```.```import-values``` field, via YAML list.

```yaml
# PARENT Chart.yaml
dependencies:
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    import-values:
      - data
```

```yaml
# CHILD Chart.yaml
exports:
  data:
    myint: 99
```

```yaml
# Final values:
myint: 99
# data is not contained in the parent final values. 
```

## Templates and Values 

- Written in Go template language
  - pkg.go.dev/text/template
- Files stored in ```templates/``` folder
- Values for the templates
  - from values.yaml inside a chart 
  - from YAML file provided on the ```helm install```

### Templates

- Supplied via values.yaml file (or --set flag)
- are accessible from the ```.Values``` object in a template
- case sensitive

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: deis-database
  namespace: deis
  labels:
    app.kubernetes.io/managed-by: deis
spec: 
  replicas: 1
  selector:
    app.kubernetes.io/name: deis-database
  template:
    metadata:
      labels:
        app.kubernetes.io/name: deis-database
    spec: 
      serviceAccount: deis-database
      containers:
        - name: deis-database
          image: {{ .Values.imageRegistry }}/postgres:{{ .Values.dockerTag  }}
          imagePullPolicy: {{ .Values.pullPolicy  }}
          ports:
            - containerPort: 5432
          env:
            - name: DATABASE_STORAGE
              value: {{ default "minio" .Values.storage  }}
```

All the values {{   }} are defined by the template author.

### Predefined

- available to every template
- cannot be overridden
- case sensitive

- Release.Name: name of the reelase (not the chart)
- Release.Namespace: namespace the chart was released to 
- Release.Service: Service that conducted the release
- Release.IsUpgrade: set to true if current operation is an upgrade or rollback
- Release.IsInstall
- Chart: Contents of the Chart.yaml
  - Chart.Version
  - Chart.Mantainers
  - so on...
- Files: map-like object containning all non-special files in the chart 
  - {{ .Files "file.name:  }}
  - {{ .Files.Get filename }}
  - {{ .Files.GetBytes  }}: Access to the contennt of the files as []byte
- CapabilitiesL map-like that contains info about the versions of K8s 


## Values files 

- ```values.yaml```
- supplies the necessary values 
- **A chart may include a default ```values.yaml``` file**
- Install command allows a user to override values by supplying additional YAML valus 
  - ```helm install --generate name --values=myvals.yaml wordpress```
  - will be merged into the default values file and override the values
- default values file must be ```values.yaml```
- the command line specified files can be named anything 
- the --set values are converted to YAML on the client side 
  
```yaml
imageRegistry: "quay.io.deis"
dockerTag: "latest"
pullPolicy: "Always" 
storage: "s3"
```

### Scope, dependencies and values 

- Values fiels can declare values for
  - top-level chart 
  - all dependencies includes in the top-level chart

```yaml
title: "My Wordpress Site" #WordPress Template 

global: # special values
  app: MyWordPress

mysql: 
  max_connections: 100
  password: "secret"

apache:
  port: 8080
```

```yaml 
# Wordpress can access to "title" and MySQL Password as 
{{ Values.title  }}
{{ Values.mysql.password  }}
# MySQL can access password using 
{{ .Values.password  }}
# MySQL cannot access to "title" property
```

- Higher level have access to all of the variables defined beneath
- Values are namespaced, and namespaced are pruned for each scope

### Global Values 

-  Available to all charts as {{ Values.global.name  }}
- The value is regenerated in each namespace 
- Useful for setting metadata properties like labels 
- **if a subchart declares a global it will be passed downward only**

### Schema Files

```yaml
{
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "image": {
      "description": "Container Image",
      "properties": {
        "repo": {
          "type": "string"
        },
        "tag": {
          "type": "string"
        }
      },
      "type": "object"
    },
    "name": {
      "description": "Service name",
      "type": "string"
    },
    "port": {
      "description": "Port",
      "minimum": 0,
      "type": "integer"
    },
    "protocol": {
      "type": "string"
    }
  },
  "required": [
    "protocol",
    "port"
  ],
  "title": "Values",
  "type": "object"
}
```

- JSON Schema
- The schema will be applied to the values to validate it 
- Validation occurs when ```helm install, upgrade, lint or template``` are invoked.
- The schema is applied to the final .Values object, so is a value is not passed in the values.yaml can be in --set flag 
- If a subvchart has a requirement that is not met in the subchart values.yaml file, the parent chart must satisfyt those restrictions 
- .Values is checked agains all subchart shcemas

## Using Helm to Manage Charts 

```sh
helm create mychart 
helm package mychart
helm lint mychart # help to find issues in the chart 
```

## Chart Repos

- HTTP Server that houses one or more packaged charts 
- Any HTTP Server can serve YAML files to be used as repo server 

## Chart Hooks

- Hook mechanism allow developers to intervene at certain points in a release;s life cycle 
- Works like regular templaters
- Have special annotations that causes Helm to utilize differently to templates

### Hooks 

| Annotation Value | Description|
| -- | -- |
| pre-install | Executes after templates are rendered BUT before any resources are created in K8s|
| post-install | after all resources are loaded into k8s |
| pre-delete | Executes a deletino request before any resources are deleted from k8s |
| post-delete | deletion rqst after all the release;s resources have been deleted
| pre-upgrade | Executes on an upgrade request after templates are rendered BUT before any resources are updated |
| post-upgrade | Exec an upgrade after all resources have been upgraded |
| pre-rollback | rollback request after templates are rendered BUY before any res are rolled back |
| post-rollback | on a rollback request after all resources have been modified |
| test | when test subcmd is invoked |

-------------

# Chart Development

- Go Templates for templating resource files 
- Two tempalte functions
  - ```include```: allows to bring in another template
  - ```required```: declare a particular values required for template rendering. If value is empty, rendering will fail

```yaml
value: {{ include "mytpl" . | lower | quote  }}
value: {{ required "A valid .Values.who entry required!" .Values.who  }}

# Strings
name: {{ .Values.MyName | quote }} # Safer quoting the strings 
port: {{.Values.Port }}
