## Kubernetes Native app Architecture

- Persistence
  - Service
  - Secret
  - Depoloyment
    - MariaDB, MongoDB, etc
- Backend Tier
  - Service
  - Config Map
  - Deployment
    - WordPress
- Frontend
  - Service
  - Deployment
    - Nginx

## Helm

- Helm is a tool for managing apps that run in K8s cluster manager.
- Provides a set of operations useful for mamaging apps

### Helm Charts: Apps Ready-to-deploy

- Packages of configured Kubernetes resources
- Elements:
  - Description of the package: chart.yaml
  - One or more templates which contains K8s manifest files
- Bitnami provides a set of stable and tested charts to deploy popular software applications 

## Minikube

- Official way to run K8s locally
- single-node K8s cluster inside a VM on the computer 

## 1. Config a Kubernetes Cluster 

1. Install Minikube
2. Create a Kubernetes cluster: ```minikube start```
3. Install ```kubectl``` CLI tool

```sh
kubectl version --short
kubectl cluster-info
kubectl get nodes
kubectl describe node
```

## 2. Install and Config Helm

4. Install Helm
5. Add bitnamy repository to helm: ```helm repo add bitnami https://charts.bitnami.com/bitnami```
6. executing ```helm install``` command the app will be deployed on K8s cluster.

```sh
helm install redis bitnami/redis # Cloud platform
helm install redis bitnami/redis --set serviceType=nodePort # minikube
```

```
## REDIS ##

output:
NAME: redis
NAMESPACE: default
STATUS: deployed
CHART NAME: redis

Redis&reg can be accessed on the following DNS names from within the cluster
redis-master.default.svc.cluster.local R/W operations (PORT 6379)
redis-replicas.default.svc.cluster.local:6379 for read only operations

To get password run:
export REDIS_PASSWORD=$(kubectl get secret --namespace default redis -o jsonpath="{.data.redis-password}" | base64 -d)

To connect to Redis&reg; server:

1.  Run a Redis&reg; pod that you can use as a client 
    kubectl run --namespace default redis-client --restart='Never' --env REDIS_PASSWORD=$REDIS_PASSWORD --image docker.io/bitnami/redis:7.0.5-debian-11-r7 --command -- sleep infinity 

    Attach to the pod 
    kubectl exec --tty -i redis-client --namespace default -- bash 

2.  Conect using the Redis&Regi CLI:
    REDISCLI_AUTH="$REDIS_PASSWORD" redis-cli -h redis-master 
    REDISCLI_AUTH="$REDIS_PASSWORD" redis-cli -h redis-replicas

To connect to the database from outside the cluster 
    kubectl port-forward --namespace default svc/redis-master 6379:6379 & REDISCLI_AUTH=$REDIS_PASSWORD" redis-cli -h 127.0.0.1 -p 6379

## MONGO ##

MongoDB&reg; can be accessed on the following DNS name(s) and ports from within your cluster:

    mongodb.default.svc.cluster.local

To get the root password run:

    export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace default mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 -d)

To connect to your database, create a MongoDB&reg; client container:

    kubectl run --namespace default mongodb-client --rm --tty -i --restart='Never' --env="MONGODB_ROOT_PASSWORD=$MONGODB_ROOT_PASSWORD" --image docker.io/bitnami/mongodb:6.0.2-debian-11-r1 --command -- bash

Then, run the following command:
    mongosh admin --host "mongodb" --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/mongodb 27017:27017 &
    mongosh --host 127.0.0.1 --authenticationDatabase admin -p $MONGODB_ROOT_PASSWORD

## ODOO ##

NAME: odoo
LAST DEPLOYED: Mon Oct 24 14:55:37 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: odoo
CHART VERSION: 21.6.5
APP VERSION: 15.0.20221010

** Please be patient while the chart is being deployed **

1. Get the Odoo URL by running:

** Please ensure an external IP is associated to the odoo service before proceeding **
** Watch the status using: kubectl get svc --namespace default -w odoo **

  export SERVICE_IP=$(kubectl get svc --namespace default odoo --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
  echo "Odoo URL: http://$SERVICE_IP/"

2. Obtain the login credentials

  export ODOO_EMAIL=user@example.com
  export ODOO_PASSWORD=$(kubectl get secret --namespace "default" odoo -o jsonpath="{.data.odoo-password}" | base64 -d)

  echo Email   : $ODOO_EMAIL
  echo Password: $ODOO_PASSWORD

## WORDPRESS ##


Your WordPress site can be accessed through the following DNS name from within your cluster:

    my-release-wordpress.default.svc.cluster.local (port 80)

To access your WordPress site from outside the cluster follow the steps below:

1. Get the WordPress URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w my-release-wordpress'

   export SERVICE_IP=$(kubectl get svc --namespace default my-release-wordpress --include "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
   echo "WordPress URL: http://$SERVICE_IP/"
   echo "WordPress Admin URL: http://$SERVICE_IP/admin"

2. Open a browser and access WordPress using the obtained URL.

3. Login with the following credentials below to see your blog:

  echo Username: user
  echo Password: $(kubectl get secret --namespace default my-release-wordpress -o jsonpath="{.data.wordpress-password}" | base64 -d)



```

```
helm install mongodb bitnami/mongodb --set serviceType=NodePort
helm install odoo bitnami/odoo --set serviceType=NodePort
helm install wordpress bitnami/wordpress --set mariadb.image=bitnami/mariadb:10.1.21-r0 --set serviceType=NodePort
```