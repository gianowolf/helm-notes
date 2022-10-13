# Upgrade Installation 

```sh
helm repo update
helm upgrade --namespace defautl mydbl bitnami/m
```

## Upgrade 

```values.yaml
auth:
  root.Password: "test1234"
```

```sh
helm update 
helm upgrade --help
>> helm upgrade mydb bitnami/mysql --set auth.rootPassword=$ROOT_PASSWORD 
helm upgrade mydb bitnami/mysql --values /path/to/file/values.yaml
```

```sh
# reuse values in a upgrade
helm update
helm upgrade mydb bitnami/mysql --reuse-values
```