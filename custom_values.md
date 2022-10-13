# Providing Custom Values 

## --set 

```sh
helm install mydb bitnami/mysql --set auth.rootPassword=test1234
```

## Recommended

```
helm install mydb bitnami/mysql --values path/to/file/values.yaml
```

### Setup config-file

```
# values.yaml
auth:
    rootPassword: "test1234"
```


