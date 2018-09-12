# Kubernetes Objects Services

## Part I

```
# Part I

## Create frontend deployment
kubectl create -f part-i/frontend-deployment.yaml
## Get Pod Name & Port-Forwarding
kubectl port-forward $(kubectl get pods -l app=guestbook,tier=frontend -o jsonpath="{.items[0].metadata.name}") 3000:3000 &
curl 127.0.0.1:3000
## Acces via browser to 127.0.0.1:3000

## Create backend deployment
kubectl create -f part-i/redis-master-deployment.yaml
kubectl create -f part-i/redis-slave-deployment.yaml
```



```
user@workstation:~/bitnami/intel-training-1/guestbook$ ls
commands.bash  part-i  part-ii  part-iii
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-i/frontend-deployment.yaml
deployment.extensions/guestbook created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl port-forward $(kubectl get pods -l app=guestbook,tier=frontend -o jsonpath="{.items[0].metadata.name}") 3000:3000 &
[2] 1737
user@workstation:~/bitnami/intel-training-1/guestbook$ error: unable to forward port because pod is not running. Current status=Pending

[2]+  Exit 1                  kubectl port-forward $(kubectl get pods -l app=guestbook,tier=frontend -o jsonpath="{.items[0].metadata.name}") 3000:3000
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl port-forward $(kubectl get pods -l app=guestbook,tier=frontend -o jsonpath="{.items[0].metadata.name}") 3000:3000 &
[2] 1793
user@workstation:~/bitnami/intel-training-1/guestbook$ Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Go to http://127.0.0.1:3000/

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-i/redis-master-deployment.yaml
deployment.extensions/redis-master created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-i/redis-slave-deployment.yaml
deployment.extensions/redis-slave created
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
foppish-jackal-redis-master-0                1/1       Running             0          14h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          14h
guestbook-bb55b9bcf-5jcwm                    1/1       Running             0          2m
guestbook-bb55b9bcf-t8h9x                    1/1       Running             0          2m
guestbook-bb55b9bcf-tnx4g                    1/1       Running             0          2m
mongo                                        1/1       Running             0          1h
nginx-5c6cb7bc9f-4chsb                       1/1       Running             0          59m
nginx-5c6cb7bc9f-fwfcs                       1/1       Running             0          58m
nginx-5c6cb7bc9f-m2hvb                       1/1       Running             0          58m
nginx-5c6cb7bc9f-vd7t2                       1/1       Running             0          59m
redis-master-5d4c55b49d-c7ckn                0/1       ContainerCreating   0          12s
redis-slave-55d7485bf7-6z4zw                 0/1       ContainerCreating   0          8s
redis-slave-55d7485bf7-8h7z5                 0/1       ContainerCreating   0          8s
redis-slave-55d7485bf7-kk627                 0/1       ContainerCreating   0          8s
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

## Part II

> [Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#service-v1-core)

Services

- ClusterIP
- NodePort
- LoadBalancer

```
# Part II

## Create backend services
kubectl create -f part-ii/redis-master-service.yaml
kubectl create -f part-ii/redis-slave-service.yaml
## Create frontend services
kubectl create -f part-ii/frontend-service.yaml
## Get NODE_PORT and access frontend
curl $(minikube ip):$(kubectl get svc frontend -o jsonpath="{.spec.ports[0].nodePort}")
## Acces via browser to: 
echo "$(minikube ip):$(kubectl get svc frontend -o jsonpath="{.spec.ports[0].nodePort}")"
```

### Nginx Service

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
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         15h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         15h
http                          NodePort    10.101.117.205   <none>        8080:30522/TCP   1h
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          15h
nginx                         ClusterIP   10.107.247.116   <none>        80/TCP           51s
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete -f resources/nginx-svc.yaml
service "nginx" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         15h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         15h
http                          NodePort    10.101.117.205   <none>        8080:30522/TCP   1h
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          15h
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
drone                                        0/1       CrashLoopBackOff   6          32m
foppish-jackal-redis-master-0                1/1       Running            0          15h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running            1          15h
guestbook-bb55b9bcf-5jcwm                    1/1       Running            0          1h
guestbook-bb55b9bcf-t8h9x                    1/1       Running            0          1h
guestbook-bb55b9bcf-tnx4g                    1/1       Running            0          1h
mongo                                        1/1       Running            0          3h
nginx-5c6cb7bc9f-4chsb                       1/1       Running            0          2h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running            0          2h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running            0          2h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running            0          2h
redis-master-5d4c55b49d-c7ckn                1/1       Running            0          1h
redis-slave-55d7485bf7-6z4zw                 0/1       CrashLoopBackOff   20         1h
redis-slave-55d7485bf7-8h7z5                 0/1       CrashLoopBackOff   20         1h
redis-slave-55d7485bf7-kk627                 0/1       CrashLoopBackOff   20         1h
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl expose deployment/nginx --port=8080 --name=http --type=NodePort
Error from server (AlreadyExists): services "http" already exists
user@workstation:~/bitnami/intel-training-1$ kubectl delete service http
service "http" deleted
user@workstation:~/bitnami/intel-training-1$ kubectl expose deployment/nginx --port=8080 --name=http --type=NodePort
service/http exposed
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         15h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         15h
http                          NodePort    10.99.38.41      <none>        8080:32514/TCP   11s
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          15h
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ curl http://$(minikube ip):$(kubectl get svc http -o jsonpath='{.spec.ports[0].nodePort}')
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get svc http -o jsonpath='{.spec.ports[0].nodePort}'
32514
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         15h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         15h
http                          NodePort    10.99.38.41      <none>        8080:32514/TCP   4m
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          15h
user@workstation:~/bitnami/intel-traininkubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
drone                                        0/1       CrashLoopBackOff   7          36m
foppish-jackal-redis-master-0                1/1       Running            0          15h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running            1          15h
guestbook-bb55b9bcf-5jcwm                    1/1       Running            0          1h
guestbook-bb55b9bcf-t8h9x                    1/1       Running            0          1h
guestbook-bb55b9bcf-tnx4g                    1/1       Running            0          1h
mongo                                        1/1       Running            0          3h
nginx-5c6cb7bc9f-4chsb                       1/1       Running            0          2h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running            0          2h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running            0          2h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running            0          2h
redis-master-5d4c55b49d-c7ckn                1/1       Running            0          1h
redis-slave-55d7485bf7-6z4zw                 0/1       CrashLoopBackOff   21         1h
redis-slave-55d7485bf7-8h7z5                 0/1       CrashLoopBackOff   21         1h
redis-slave-55d7485bf7-kk627                 0/1       CrashLoopBackOff   21         1h
user@workstation:~/bitnami/intel-training-1$ 
```

## Part III

```
# Part III

## Remove old frontend service
kubectl delete svc frontend
## Create frontend service
kubectl create -f part-iii/frontend-service.yaml
kubectl create -f part-iii/ingress.yaml
## Edit hosts
## Windows Users: https://blog.kowalczyk.info/article/10c/local-dns-modifications-on-windows-etchosts-equivalent.html
echo "$(minikube ip)   guestbook-site.com" | sudo tee -a /etc/hosts
## Acces via browser to guestbook-site.com
```
