# Pre-Work

## Kubernetes

> Bitnami now provides Kubernetes Training, and we would love to see you there. Please follow the guide below to ensure that you are ready to go for our Kubernetes training. Interested in attending one of our public trainings, or reserving a private training for your team? Check out our latest schedule or contact us on our training page. [Discovering Kubernetes with Bitnami](https://engineering.bitnami.com/articles/discovering-kubernetes-with-bitnami.html)

- [Minikube](https://github.com/kubernetes/minikube)
- [Kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
- [K8S Training](https://github.com/bitnami-labs/k8s-training)
- [K8S Training Resources](https://github.com/bitnami-labs/k8s-training-resources)
- [Helm Charts](https://github.com/helm/charts)

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

## Kubernetes Pods

```
######
# Pods
######
kubectl create -f resources/mongo-pod.yaml
kubectl get pods
## Labels
kubectl label pods mongo foo=bar
kubectl get pods --show-labels
kubectl get pods -l foo=bar
kubectl get pods -Lfoo
```

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

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete rs nginx
replicaset.extensions "nginx" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS        RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running       0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running       1          12h
mongo                                        1/1       Running       0          31m
nginx-b889q                                  0/1       Terminating   0          2m
nginx-j4lf9                                  0/1       Terminating   0          4m
nginx-pd27d                                  0/1       Terminating   0          5m
user@workstation:~/bitnami/intel-training-1$ 
```

## Kubernetes Deployment

> [Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#replicaset-v1-apps)

```
#############
# Deployments
#############
kubectl create -f resources/nginx-deployment.yaml
kubectl scale deployment nginx --replicas=4
kubectl get pods --watch
kubectl set image deployment nginx nginx=bitnami/nginx:1.14.0-r49 --all
kubectl get rs --watch
kubectl get pods --watch
kubectl edit deployment nginx
```

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  revisionHistoryLimit: 2
  strategy:
    type: RollingUpdate
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: bitnami/nginx
        name: nginx
        ports:
        - name: http
          containerPort: 8080
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/nginx-deployment.yaml
deployment.extensions/nginx created
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl scale deployment nginx --replicas=4
deployment.extensions/nginx scaled
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running             0          12h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          12h
mongo                                        1/1       Running             0          42m
nginx-858d9d4fff-flj22                       1/1       Running             0          16s
nginx-858d9d4fff-ltqb4                       1/1       Running             0          5s
nginx-858d9d4fff-vqkr5                       1/1       Running             0          16s
nginx-858d9d4fff-wcz76                       0/1       ContainerCreating   0          5s
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl set image deployment nginx nginx=bitnami/nginx:1.14.0-r49 --all
deployment.extensions/nginx image updated
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get rs --watch
NAME                                   DESIRED   CURRENT   READY     AGE
foppish-jackal-redis-slave-58b8f6b7f   1         1         1         13h
nginx-858d9d4fff                       3         3         3         2m
nginx-86f798ff45                       2         2         0         5s
...
```

### Kubernetes Deployment Rollback

```
# Rollbacks
kubectl run dokuwiki --image=bitnami/dokuwiki --record
kubectl get deployments dokuwiki -o yaml
kubectl set image deployment/dokuwiki dokuwiki=bitnami/dokuwiki:09 --all
kubectl rollout history deployment/dokuwiki
kubectl get pods
kubectl rollout undo deployment/dokuwiki
kubectl rollout history deployment/dokuwiki
kubectl delete deploy dokuwiki
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl run dokuwiki --image=bitnami/dokuwiki --record
deployment.apps/dokuwiki created
user@workstation:~/bitnami/intel-training-1$ kubectl get deployments dokuwiki -o yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubernetes.io/change-cause: kubectl run dokuwiki --image=bitnami/dokuwiki --record=true
  creationTimestamp: 2018-09-12T16:30:16Z
  generation: 1
  labels:
    run: dokuwiki
  name: dokuwiki
  namespace: default
  resourceVersion: "13646"
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/dokuwiki
  uid: 217532a5-b6a9-11e8-b38d-08002736b0e9
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 2
  selector:
    matchLabels:
      run: dokuwiki
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: dokuwiki
    spec:
      containers:
      - image: bitnami/dokuwiki
        imagePullPolicy: Always
        name: dokuwiki
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  conditions:
  - lastTransitionTime: 2018-09-12T16:30:16Z
    lastUpdateTime: 2018-09-12T16:30:16Z
    message: Deployment does not have minimum availability.
    reason: MinimumReplicasUnavailable
    status: "False"
    type: Available
  - lastTransitionTime: 2018-09-12T16:30:16Z
    lastUpdateTime: 2018-09-12T16:30:16Z
    message: ReplicaSet "dokuwiki-774cb5b777" is progressing.
    reason: ReplicaSetUpdated
    status: "True"
    type: Progressing
  observedGeneration: 1
  replicas: 1
  unavailableReplicas: 1
  updatedReplicas: 1
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl set image deployment/dokuwiki dokuwiki=bitnami/dokuwiki:09 --all
deployment.extensions/dokuwiki image updated
user@workstation:~/bitnami/intel-training-1$ kubectl rollout history deployment/dokuwiki
deployments "dokuwiki"
REVISION  CHANGE-CAUSE
1         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true
2         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true

```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS         RESTARTS   AGE
dokuwiki-67c99b5f8-5hp5q                     0/1       ErrImagePull   0          46s
dokuwiki-774cb5b777-dkb9v                    1/1       Running        0          2m
foppish-jackal-redis-master-0                1/1       Running        0          13h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running        1          13h
mongo                                        1/1       Running        0          56m
nginx-5c6cb7bc9f-4chsb                       1/1       Running        0          11m
nginx-5c6cb7bc9f-fwfcs                       1/1       Running        0          11m
nginx-5c6cb7bc9f-m2hvb                       1/1       Running        0          11m
nginx-5c6cb7bc9f-vd7t2                       1/1       Running        0          11m
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl rollout undo deployment/dokuwiki
deployment.extensions/dokuwiki
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl rollout history deployment/dokuwiki
deployments "dokuwiki"
REVISION  CHANGE-CAUSE
2         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true
3         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true

```

```
user@workstation:~/bitnami/intel-training-1$ kubectl set image deployment/dokuwiki dokuwiki=bitnami/dokuwiki:09 --all --record
deployment.extensions/dokuwiki image updated
user@workstation:~/bitnami/intel-training-1$ kubectl rollout history deployment/dokuwiki
deployments "dokuwiki"
REVISION  CHANGE-CAUSE
3         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true
4         kubectl set image deployment/dokuwiki dokuwiki=bitnami/dokuwiki:09 --all=true --record=true

user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
dokuwiki-67c99b5f8-vhwng                     0/1       ImagePullBackOff   0          31s
dokuwiki-774cb5b777-dkb9v                    1/1       Running            0          6m
dokuwiki09-76cc4f5488-twwdt                  0/1       ImagePullBackOff   0          2m
foppish-jackal-redis-master-0                1/1       Running            0          13h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running            1          13h
mongo                                        1/1       Running            0          59m
nginx-5c6cb7bc9f-4chsb                       1/1       Running            0          14m
nginx-5c6cb7bc9f-fwfcs                       1/1       Running            0          14m
nginx-5c6cb7bc9f-m2hvb                       1/1       Running            0          14m
nginx-5c6cb7bc9f-vd7t2                       1/1       Running            0          14m
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl rollout undo deployment/dokuwiki
deployment.extensions/dokuwiki
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl rollout history deployment/dokuwiki
deployments "dokuwiki"
REVISION  CHANGE-CAUSE
4         kubectl set image deployment/dokuwiki dokuwiki=bitnami/dokuwiki:09 --all=true --record=true
5         kubectl run dokuwiki --image=bitnami/dokuwiki --record=true

user@workstation:~/bitnami/intel-training-1$ 
```

## Kubernetes Tips and Tricks

```
$ kubectl config view
$ kubectl config use-context
$ kubectl annotate
$ kubectl label
$ kubectl create -f ./<DIR>
$ kubectl create -f <URL>
$ kubectl edit ...
$ kubectl proxy ...
$ kubectl exec ...
$ kubectl logs ...
$ kubectl get pods,svc,deployments
$ kubectl --v=99 ...
$ kubectl describe ...
```

## Kubernetes Services

```
##########
# Services
##########
kubectl create -f resources/nginx-svc.yaml
kubectl get svc
kubectl delete -f resources/nginx-svc.yaml
kubectl expose deployment/nginx --port=8080 --name=http --type=NodePort
kubectl get svc
curl http://$(minikube ip):$(kubectl get svc nginx -o jsonpath='{.spec.ports[0].nodePort}')
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/nginx-svc.yaml
service/nginx created
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP   13h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP   13h
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP    14h
nginx                         ClusterIP   10.111.72.167    <none>        80/TCP     4s
user@workstation:~/bitnami/intel-training-1$ kubectl delete -f resources/nginx-svc.yaml
service "nginx" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl expose deployment/nginx --port=8080 --name=http --type=NodePort
service/http exposed
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         13h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         13h
http                          NodePort    10.101.117.205   <none>        8080:30522/TCP   4s
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          14h
user@workstation:~/bitnami/intel-training-1$ curl http://$(minikube ip):$(kubectl get svc nginx -o jsonpath='{.spec.ports[0].nodePort}')
Error from server (NotFound): services "nginx" not found
curl: (7) Failed to connect to 192.168.99.100 port 80: Connection refused
user@workstation:~/bitnami/intel-training-1$ 
```
