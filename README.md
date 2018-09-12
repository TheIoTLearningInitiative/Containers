# Pre-Work

## Kubernetes

> Bitnami now provides Kubernetes Training, and we would love to see you there. Please follow the guide below to ensure that you are ready to go for our Kubernetes training. Interested in attending one of our public trainings, or reserving a private training for your team? Check out our latest schedule or contact us on our training page. [Discovering Kubernetes with Bitnami](https://engineering.bitnami.com/articles/discovering-kubernetes-with-bitnami.html)

- [Minikube](https://github.com/kubernetes/minikube)
- [Kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)

# Day 1

- [Floobits](https://floobits.com/juan131/intel-training-1/file/WELCOME.md:11)

## Minikube

> Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day. [Github](https://github.com/kubernetes/minikube)

```
minikube start
minikube status
minikube ssh
minikube dashboard
minikube addons list
minukube addons enable dashboard
```

Create bitnami/mongodb via Dashboard

```
user@workstation:~$ minikube ssh
                         _             _            
            _         _ ( )           ( )           
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __  
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

$ docker ps | grep mongodb
cfc6d56bfc89        bitnami/mongodb                            "/app-entrypoint.sh …"   2 minutes ago       Up 2 minutes                            k8s_mongodb_mongodb-698f9d9b7f-qjdh9_default_d325454c-b69b-11e8-b38d-08002736b0e9_0
d046557aea6d        k8s.gcr.io/pause-amd64:3.1                 "/pause"                 2 minutes ago       Up 2 minutes                            k8s_POD_mongodb-698f9d9b7f-qjdh9_default_d325454c-b69b-11e8-b38d-08002736b0e9_0
effc8021d725        bitnami/mongodb                            "/app-entrypoint.sh …"   12 hours ago        Up 12 hours                             k8s_kubeapps-mongodb_kubeapps-mongodb-649c64c44f-5llqh_kubeapps_115d7f9d-b63b-11e8-b38d-08002736b0e9_0
89bfa0fce567        k8s.gcr.io/pause-amd64:3.1                 "/pause"                 12 hours ago        Up 12 hours                             k8s_POD_kubeapps-mongodb-649c64c44f-5llqh_kubeapps_115d7f9d-b63b-11e8-b38d-08002736b0e9_0
$ 
```

## Kubectl

> The k8s.io/kubectl repo is used to track issues for the kubectl cli distributed with k8s.io/kubernetes. It also contains packages intended for use by client programs. [Github](https://github.com/kubernetes/kubectl)

```
kubectl config get-contexts
kubectl config use-context minikube
kubectl config current-context
kubectl cluster-info
kubectl cluster-info --help
```

```
user@workstation:~$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

## Kubernetes API

```
user@workstation:~$ kubectl proxy --port 8080 &
[1] 5642
Starting to serve on 127.0.0.1:8080
```

```
user@workstation:~$ curl http://127.0.0.1:8080/api
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "192.168.99.100:8443"
    }
  ]
}user@workstation:~$
```

## Kubernetes Objects

> This page explains how Kubernetes objects are represented in the Kubernetes API, and how you can express them in .yaml format. [Homepage](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

## Kubernetes Deploy

```
user@workstation:~$ kubectl get deploy
NAME                         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
foppish-jackal-redis-slave   1         1         1            1           12h
mongodb                      1         1         1            1           39m
user@workstation:~$ kubectl delete deploy
error: resource(s) were provided, but no name, label selector, or --all flag specified
user@workstation:~$ kubectl delete deploy mongodb
deployment.extensions "mongodb" deleted
user@workstation:~$ 
```

```
user@workstation:~/bitnami$ mkdir resources
user@workstation:~/bitnami$ nano resources/mongo-pod.yaml
user@workstation:~/bitnami$ kubectl create -f resources/mongo-pod.yaml
pod/mongo created
user@workstation:~/bitnami$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running             0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          12h
mongo                                        0/1       ContainerCreating   0          4s
user@workstation:~/bitnami$ kubectl get pods
NAME                                         READY     STATUS    RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running   0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          12h
mongo                                        1/1       Running   0          2m
user@workstation:~/bitnami$ 
```

## Kubernetes Delete

```
user@workstation:~/bitnami$ kubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
drone                                        0/1       ImagePullBackOff   0          1m
foppish-jackal-redis-master-0                1/1       Running            0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running            1          12h
mongo                                        1/1       Running            0          8m
user@workstation:~/bitnami$ kubectl delete deploy drone
Error from server (NotFound): deployments.extensions "drone" not found
user@workstation:~/bitnami$ kubectl delete pod drone
pod "drone" deleted
user@workstation:~/bitnami$ kubectl get pods
NAME                                         READY     STATUS    RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running   0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          12h
mongo                                        1/1       Running   0          10m
user@workstation:~/bitnami$ 
```

## Kubernetes Labels

```
## Labels
kubectl label pods mongo foo=bar
kubectl get pods --show-labels
kubectl get pods -l foo=bar
kubectl get pods -Lfoo
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl label pods mongo version=0.1
pod/mongo labeled
user@workstation:~/bitnami/intel-training-1$ kubectl get pods --show-labels
NAME                                         READY     STATUS    RESTARTS   AGE       LABELS
foppish-jackal-redis-master-0                1/1       Running   0          12h       app=redis,chart=redis-3.10.0,controller-revision-hash=foppish-jackal-redis-master-d7bdfc5b6,release=foppish-jackal,role=master,statefulset.kubernetes.io/pod-name=foppish-jackal-redis-master-0
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          12h       app=redis,chart=redis-3.10.0,pod-template-hash=146492639,release=foppish-jackal,role=slave
mongo                                        1/1       Running   0          15m       version=0.1
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -l version=0.1
NAME      READY     STATUS    RESTARTS   AGE
mongo     1/1       Running   0          16m
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -L version
NAME                                         READY     STATUS    RESTARTS   AGE       VERSION
foppish-jackal-redis-master-0                1/1       Running   0          12h       
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          12h       
mongo                                        1/1       Running   0          16m       0.1
```

# Kubernetes ReplicaSet

> [Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deployment-v1-apps)

```
#############
# ReplicaSets
#############
kubectl create -f resources/nginx-rs.yaml
kubectl scale rs nginx --replicas=5
kubectl get pods --watch
# Delete a pod and observe
kubectl get pods
kubectl delete pod $(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}') && kubectl get pods --watch
kubectl delete rs nginx
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/nginx-rs.yaml 
replicaset.extensions/nginx created
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running             0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          12h
mongo                                        1/1       Running             0          26m
nginx-cm6z5                                  0/1       ContainerCreating   0          7s
nginx-pd27d                                  0/1       ContainerCreating   0          7s
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl scale rs nginx --replicas=5
replicaset.extensions/nginx scaled
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running             0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          12h
mongo                                        1/1       Running             0          27m
nginx-cm6z5                                  1/1       Running             0          1m
nginx-j4lf9                                  0/1       ContainerCreating   0          6s
nginx-pd27d                                  1/1       Running             0          1m
nginx-r55rm                                  1/1       Running             0          6s
nginx-r856s                                  0/1       ContainerCreating   0          6s
user@workstation:~/bitnami/intel-training-1$ 
```
