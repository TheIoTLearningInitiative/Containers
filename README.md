# Pre-Work

## Kubernetes

> Bitnami now provides Kubernetes Training, and we would love to see you there. Please follow the guide below to ensure that you are ready to go for our Kubernetes training. Interested in attending one of our public trainings, or reserving a private training for your team? Check out our latest schedule or contact us on our training page. [Discovering Kubernetes with Bitnami](https://engineering.bitnami.com/articles/discovering-kubernetes-with-bitnami.html)

- [Minikube](https://github.com/kubernetes/minikube)
- [Kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)

# Day 1

- [Floobits](https://floobits.com/juan131/intel-training-1/file/WELCOME.md:11)

## Minikube

> [Homepage](https://github.com/kubernetes/minikube)

```
minikube start
minikube status
minikube ssh
minikube dashboard
minikube addons list
minukube addons enable dashboard
```

Create bitnami/mongodb

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
