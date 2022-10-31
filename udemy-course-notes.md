# Commands 

## Helm

```sh
helm repo list
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list

# Search the repository:
helm search repo mysql
helm search repo database
helm search repo database --versions

# Install a package:
kubectl get pods
#(Below Two commands - In a Different Shell/Commandline window/tab)
minikube ssh
docker images
helm install mydb bitnami/mysql

# Check the cluster:
kubectl get pods
minikube ssh
docker images

# To check the installation status:
helm status mydb
```


--------------------------------------------

To Upgrade:

ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)

helm upgrade --namespace default mysql-release bitnami/mysql --set auth.rootPassword=$ROOT_PASSWORD

-------

helm uninstall mysql-release
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