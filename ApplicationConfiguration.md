# Application Configuration

## Environment Variables

```
###################
# App Configuration
###################
kubectl create -f resources/mysql.yaml
kubectl exec -it mysql -- mysql -h 127.0.0.1
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/mysql.yaml
pod/mysql created
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1
error: unable to upgrade connection: container not found ("mysql")
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1
error: unable to upgrade connection: container not found ("mysql")
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1
error: unable to upgrade connection: container not found ("mysql")
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
busybox                                      1/1       Running             0          44m
drone                                        0/1       CrashLoopBackOff    13         1h
foppish-jackal-redis-master-0                1/1       Running             0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running             0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running             0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running             0          2h
mongo                                        1/1       Running             0          4h
mysql                                        0/1       ContainerCreating   0          28s
nginx-5c6cb7bc9f-4chsb                       1/1       Running             0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running             0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running             0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running             0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running             0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running             24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running             24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running             24         2h
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1
error: unable to upgrade connection: container not found ("mysql")
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.23 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
````

## ConfigMaps

- [Documentation](https://kubernetes.io/docs/concepts/storage/volumes/#configmap)

```
# ConfigMap
kubectl delete pod mysql
kubectl create configmap mysql --from-literal=replication-mode=master \
--from-literal=replication-user=master
kubectl create -f resources/mysql-with-cm.yaml
kubectl logs -f mysql
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete pod mysql
pod "mysql" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl create configmap mysql --from-literal=replication-mode=master \
> --from-literal=replication-user=master
configmap/mysql created
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/mysql-with-cm.yaml
pod/mysql created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS    RESTARTS   AGE
busybox                                      1/1       Running   0          48m
drone                                        1/1       Running   14         1h
foppish-jackal-redis-master-0                1/1       Running   0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running   0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running   0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running   0          2h
mongo                                        1/1       Running   0          4h
mysql                                        1/1       Running   0          6s
nginx-5c6cb7bc9f-4chsb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running   0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running   0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running   0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running   24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running   24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running   24         2h
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl logs -f mysql

Welcome to the Bitnami mysql container
Subscribe to project updates by watching https://github.com/bitnami/bitnami-docker-mysql
Submit issues and feature requests at https://github.com/bitnami/bitnami-docker-mysql/issues

nami    INFO  Initializing mysql
mysql   INFO  ==> Cleaning data dir...
mysql   INFO  ==> Configuring permissions...
mysql   INFO  ==> Validating inputs...
mysql   WARN  Allowing the "rootPassword" input to be empty
mysql   WARN  Allowing the "replicationPassword" input to be empty
mysql   INFO  ==> Initializing database...
mysql   INFO  ==> Creating 'root' user with unrestricted access...
mysql   INFO  ==> Configuring replication mode master...
mysql   INFO  ==> Creating replication user master...
mysql   INFO  ==> Running mysql upgrade...
mysql   INFO 
mysql   INFO  ########################################################################
mysql   INFO   Installation parameters for mysql:
mysql   INFO     Root User: root
mysql   INFO     Root Password: Not set during installation
mysql   INFO     Replication Mode: master
mysql   INFO     Replication Username: master
mysql   INFO     Replication Password: Not set during installation
mysql   INFO   (Passwords are not shown for security reasons)
mysql   INFO  ########################################################################
mysql   INFO 
nami    INFO  mysql successfully initialized
INFO  ==> Starting mysql... 
INFO  ==> Starting mysqld_safe...
2018-09-12T19:56:22.508109Z mysqld_safe Logging to '/opt/bitnami/mysql/logs/mysqld.log'.
2018-09-12T19:56:22.556874Z mysqld_safe Starting mysqld daemon with databases from /opt/bitnami/mysql/data
```

## Secrets

> [Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)

```
# Secrets
kubectl delete pod mysql
kubectl create secret generic mysql --from-literal=password=root
kubectl create -f resources/mysql-with-secrets.yaml
kubectl exec -it mysql -- mysql -h 127.0.0.1 -u root -proot
kubectl get secret mysql -o json | jq -r '.data.password' | base64 --decode
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete pod mysql
pod "mysql" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl create secret generic mysql --from-literal=password=root
secret/mysql created
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/mysql-with-secrets.yaml
pod/mysql created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
busybox                                      1/1       Running            0          52m
drone                                        0/1       CrashLoopBackOff   14         1h
foppish-jackal-redis-master-0                1/1       Running            0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running            1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running            0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running            0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running            0          2h
mongo                                        1/1       Running            0          4h
mysql                                        1/1       Running            0          10s
nginx-5c6cb7bc9f-4chsb                       1/1       Running            0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running            0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running            0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running            0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running            0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running            24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running            24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running            24         2h
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ sudo apt install jq
user@workstation:~/bitnami/intel-training-1$ kubectl get secret mysql -o json | jq -r '.data.password' | base64 --decode
root
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it mysql -- mysql -h 127.0.0.1 -u root -proot
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.23 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

## Namespaces

> Namespaces are intended to isolate multiple groups/teams and give them access to a set of resources.

```
############
# namespaces
############
kubectl get ns
kubectl create ns myns
kubectl get ns/myns -o yaml
cat << 'EOF' >> mongo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongo
  namespace: myns
spec:
  containers:
  - image: bitnami/mongodb
    name: mongo
EOF
kubectl create -f mongo.yaml
kubectl delete ns/myns
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get ns
NAME          STATUS    AGE
default       Active    16h
kube-public   Active    16h
kube-system   Active    16h
kubeapps      Active    16h
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create ns myns
namespace/myns created
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get ns/myns -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: 2018-09-12T20:07:47Z
  name: myns
  resourceVersion: "30331"
  selfLink: /api/v1/namespaces/myns
  uid: 848333ad-b6c7-11e8-b38d-08002736b0e9
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
```

```
user@workstation:~/bitnami/intel-training-1$ cat << 'EOF' >> mongo.yaml
> apiVersion: v1
> kind: Pod
> metadata:
>   name: mongo
>   namespace: myns
> spec:
>   containers:
>   - image: bitnami/mongodb
>     name: mongo
> EOF
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get ns
NAME          STATUS    AGE
default       Active    16h
kube-public   Active    16h
kube-system   Active    16h
kubeapps      Active    16h
myns          Active    46s
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS    RESTARTS   AGE
busybox                                      1/1       Running   1          1h
drone                                        1/1       Running   16         1h
foppish-jackal-redis-master-0                1/1       Running   0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running   0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running   0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running   0          2h
mongo                                        1/1       Running   0          4h
mysql                                        1/1       Running   0          10m
nginx-5c6cb7bc9f-4chsb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running   0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running   0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running   0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running   24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running   24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running   24         2h
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -n default
NAME                                         READY     STATUS    RESTARTS   AGE
busybox                                      1/1       Running   1          1h
drone                                        1/1       Running   17         1h
foppish-jackal-redis-master-0                1/1       Running   0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running   0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running   0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running   0          2h
mongo                                        1/1       Running   0          4h
mysql                                        1/1       Running   0          12m
nginx-5c6cb7bc9f-4chsb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running   0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running   0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running   0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running   24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running   24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running   24         2h
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -n myns
No resources found.
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f mongo.yaml 
pod/mongo created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -n myns
NAME      READY     STATUS    RESTARTS   AGE
mongo     1/1       Running   0          6s
user@workstation:~/bitnami/intel-training-1$ 
```

## Persistence

> On-disk files in a Container are ephemeral, which presents some problems for non-trivial applications when running in Containers. First, when a Container crashes, kubelet will restart it, but the files will be lost - the Container starts with a clean state. Second, when running Containers together in a Pod it is often necessary to share files between those Containers. The Kubernetes Volume abstraction solves both of these problems. [Documentation](https://kubernetes.io/docs/concepts/storage/volumes/)

```
#########
# volumes
#########
# Sharing Volumes
kubectl create -f resources/share-volume.yaml
kubectl logs -f share-volume box
kubectl delete pod share-volume
...
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/share-volume.yaml
pod/share-volume created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
busybox                                      1/1       Running             1          1h
drone                                        0/1       CrashLoopBackOff    22         2h
foppish-jackal-redis-master-0                1/1       Running             0          17h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          17h
guestbook-bb55b9bcf-5jcwm                    1/1       Running             0          3h
guestbook-bb55b9bcf-t8h9x                    1/1       Running             0          3h
guestbook-bb55b9bcf-tnx4g                    1/1       Running             0          3h
mongo                                        1/1       Running             0          5h
mysql                                        1/1       Running             0          47m
nginx-5c6cb7bc9f-4chsb                       1/1       Running             0          4h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running             0          4h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running             0          4h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running             0          4h
redis-master-5d4c55b49d-c7ckn                1/1       Running             0          3h
redis-slave-55d7485bf7-6z4zw                 1/1       Running             24         3h
redis-slave-55d7485bf7-8h7z5                 1/1       Running             24         3h
redis-slave-55d7485bf7-kk627                 1/1       Running             24         3h
share-volume                                 0/2       ContainerCreating   0          5s
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl logs -f share-volume box
hello buddy!
hello buddy!
hello buddy!
hello buddy!
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete pod share-volume
pod "share-volume" deleted
```

### Persistent Volume Claim

Developer

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

```
apiVersion: v1
kind: Pod
metadata:
  name: example
containers:
  - image: busybox
    volumeMounts:
    - mountPath: /busy
      name: test
    name: busy
  volumes:
  - name: test
    persistentVolumeClaim:
        claimName: myclaim
```

Static Method :: Administrator

```
apiVersion: "v1"
kind: "PersistentVolume"
metadata:
  name: "pv0001" 
spec:
  capacity:
    storage: "10Gi" 
  accessModes:
    - "ReadWriteOnce"
  gcePersistentDisk: 
    fsType: "ext4" 
    pdName: "pd-disk-1" 
```

```
apiVersion: "v1"
kind: "PersistentVolume"
metadata:
  name: "pv0001" 
  labels: 
     type: local
spec:
  capacity:
    storage: "10Gi" 
  accessModes:
    - "ReadWriteOnce"
  hostpath: 
    path: "/mnt/data" 
```

Static Method :: Developer

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: myclaim2
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
selector:
    matchLabels:
      type: "gce"
```

Dynamic Method :: Administrator

```
  "pv0001" 
  "30Gi" 
  "ReadWriteOnce"
```

```
  "pv0002" 
  “gcePersistentDisk”
  "30Gi" 
  "ReadWriteOnce"
```

Dynamic Method :: Developer

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Gi
```

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: myclaim2
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Gi
  storageClass: gce-pd
```

### MariaDB with persistence

```
#########
# volumes
#########
...
# MariaDB with persistence
kubectl create -f resources/mariadb-pvc.yaml
kubectl get pvc
kubectl create -f resources/mariadb-persitence.yaml
kubectl exec -it $(kubectl get pods -l app=mariadb -o jsonpath='{.items[0].metadata.name}') bash
mysql
show databases;
create database intel_database;
kubectl delete pod $(kubectl get pods -l app=mariadb -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $(kubectl get pods -l app=mariadb -o jsonpath='{.items[0].metadata.name}') bash
mysql
show databases;
kubectl delete pvc mariadb-data
kubectl delete deploy mariadb
```
