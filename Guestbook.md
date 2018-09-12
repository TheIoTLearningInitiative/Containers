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
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: default
spec:
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: http
  selector:
    app: nginx
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

Go to http://192.168.99.100:32514/

### DNS

```
# DNS
kubectl create -f resources/busybox.yaml
kubectl exec -it busybox -- nslookup nginx
kubectl get svc
kubectl exec -ti busybox -- wget -O- http://nginx:80
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/busybox.yaml
pod/busybox created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods
NAME                                         READY     STATUS              RESTARTS   AGE
busybox                                      0/1       ContainerCreating   0          4s
drone                                        0/1       CrashLoopBackOff    8          45m
foppish-jackal-redis-master-0                1/1       Running             0          15h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running             1          15h
guestbook-bb55b9bcf-5jcwm                    1/1       Running             0          1h
guestbook-bb55b9bcf-t8h9x                    1/1       Running             0          1h
guestbook-bb55b9bcf-tnx4g                    1/1       Running             0          1h
mongo                                        1/1       Running             0          3h
nginx-5c6cb7bc9f-4chsb                       1/1       Running             0          2h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running             0          2h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running             0          2h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running             0          2h
redis-master-5d4c55b49d-c7ckn                1/1       Running             0          1h
redis-slave-55d7485bf7-6z4zw                 0/1       CrashLoopBackOff    22         1h
redis-slave-55d7485bf7-8h7z5                 0/1       CrashLoopBackOff    22         1h
redis-slave-55d7485bf7-kk627                 0/1       CrashLoopBackOff    22         1h
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl exec -it busybox -- nslookup http.default.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53


*** Can't find http.default.svc.cluster.local: No answer

user@workstation:~/bitnami/intel-training-1$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         15h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         15h
http                          NodePort    10.99.38.41      <none>        8080:32514/TCP   13m
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          15h
user@workstation:~/bitnami/intel-training-1$ 
```

Error!

## Part II

> [Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#service-v1-core)

### Services

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

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-ii/redis-master-service.yaml
service/redis-master created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-ii/redis-slave-service.yaml
service/redis-slave created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-ii/frontend-service.yaml
service/frontend created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get pods
NAME                                         READY     STATUS             RESTARTS   AGE
busybox                                      1/1       Running            0          7m
drone                                        0/1       CrashLoopBackOff   9          52m
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
redis-slave-55d7485bf7-6z4zw                 0/1       CrashLoopBackOff   23         1h
redis-slave-55d7485bf7-8h7z5                 0/1       CrashLoopBackOff   23         1h
redis-slave-55d7485bf7-kk627                 1/1       Running            24         1h
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ curl $(minikube ip):$(kubectl get svc frontend -o jsonpath="{.spec.ports[0].nodePort}")
```

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
    <meta charset="utf-8">
    <meta content="width=device-width" name="viewport">
    <link href="style.css" rel="stylesheet">
    <title>Guestbook</title>
  </head>
  <body>
    <div id="header">
      <h1>Guestbook</h1>
    </div>

    <div id="guestbook-entries">
      <p>Waiting for database connection...</p>
    </div>

    <div>
      <form id="guestbook-form">
        <input autocomplete="off" id="guestbook-entry-content" type="text">
        <a href="#" id="guestbook-submit">Submit</a>
      </form>
    </div>

    <div>
      <p><h2 id="guestbook-host-address"></h2></p>
      <p><a href="env">/env</a>
      <a href="info">/info</a></p>
    </div>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
    <script src="script.js"></script>
  </body>
</html>
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ echo "$(minikube ip):$(kubectl get svc frontend -o jsonpath="{.spec.ports[0].nodePort}")"
192.168.99.100:30920
```

Go to http://127.0.0.1:3000/

## Ingress Controller

> [Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-controllers)

```
user@workstation:~/bitnami/intel-training-1/guestbook$ sudo cat /etc/hosts
[sudo] password for user: 
127.0.0.1       localhost
127.0.1.1       workstation

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

- [Ingress Nginx Annotations](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md)

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook-ingress
  annotations:
    nginx.ingress.kubernetes.io/affinity: cookie
spec:
  rules:
  - host: guestbook-site.com
    http:
      paths:
      - path: "/"
        backend:
          serviceName: frontend
          servicePort: 80
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get deployments --all-namespaces
NAMESPACE     NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
default       foppish-jackal-redis-slave          1         1         1            1           16h
default       guestbook                           3         3         3            3           2h
default       nginx                               4         4         4            4           3h
default       redis-master                        1         1         1            1           2h
default       redis-slave                         3         3         3            3           2h
kube-system   kube-dns                            1         1         1            1           16h
kube-system   kubernetes-dashboard                1         1         1            1           16h
kube-system   tiller-deploy                       1         1         1            1           16h
kubeapps      kubeapps                            1         1         1            1           16h
kubeapps      kubeapps-apprepository-controller   1         1         1            1           16h
kubeapps      kubeapps-chartsvc                   1         1         1            1           16h
kubeapps      kubeapps-dashboard                  1         1         1            1           16h
kubeapps      kubeapps-mongodb                    1         1         1            1           16h
kubeapps      kubeapps-tiller-proxy               1         1         1            1           16h
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get deployments -n kube-system
NAME                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
kube-dns               1         1         1            1           16h
kubernetes-dashboard   1         1         1            1           16h
tiller-deploy          1         1         1            1           16h
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
#########
# Ingress
#########
## Installation
minikube addons list
minikube addons enable ingress
kubectl get deployments -n kube-system
kubectl get deployment nginx-ingress-controller -n kube-system -o yaml
## Use
kubectl create -f resources/ingress.yaml
echo "$(minikube ip)   foo.bar.com" | sudo tee -a /etc/hosts
curl http://foo.bar.com
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ minikube addons list
- addon-manager: enabled
- coredns: disabled
- dashboard: enabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- heapster: disabled
- ingress: disabled
- kube-dns: enabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
user@workstation:~/bitnami/intel-training-1/guestbook$ minikube addons enable ingress
ingress was successfully enabled
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get deployments -n kube-system
NAME                       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
default-http-backend       1         1         1            0           14s
kube-dns                   1         1         1            1           16h
kubernetes-dashboard       1         1         1            1           16h
nginx-ingress-controller   1         1         1            0           14s
tiller-deploy              1         1         1            1           16h
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get deployment nginx-ingress-controller -n kube-system -o yaml
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f resources/ingress.yaml
ingress.extensions/nginx created
user@workstation:~/bitnami/intel-training-1$ echo "$(minikube ip)   foo.bar.com" | sudo tee -a /etc/hosts
192.168.99.100   foo.bar.com
user@workstation:~/bitnami/intel-training-1$ curl http://foo.bar.com
curl: (7) Failed to connect to foo.bar.com port 80: Connection refused
```

```
user@workstation:~/bitnami/intel-training-1$ curl http://foo.bar.com
<html>
<head><title>503 Service Temporarily Unavailable</title></head>
<body bgcolor="white">
<center><h1>503 Service Temporarily Unavailable</h1></center>
<hr><center>nginx/1.13.12</center>
</body>
</html>
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

```
user@workstation:~/bitnami/intel-training-1$ kubectl delete svc frontend
service "frontend" deleted
user@workstation:~/bitnami/intel-training-1$ cd guestbook/
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-iii/frontend-service.yaml
service/frontend created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl create -f part-iii/ingress.yaml
ingress.extensions/guestbook-ingress created
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get pods
NAME                                         READY     STATUS    RESTARTS   AGE
busybox                                      1/1       Running   0          39m
drone                                        1/1       Running   13         1h
foppish-jackal-redis-master-0                1/1       Running   0          16h
foppish-jackal-redis-slave-58b8f6b7f-hrm6p   1/1       Running   1          16h
guestbook-bb55b9bcf-5jcwm                    1/1       Running   0          2h
guestbook-bb55b9bcf-t8h9x                    1/1       Running   0          2h
guestbook-bb55b9bcf-tnx4g                    1/1       Running   0          2h
mongo                                        1/1       Running   0          4h
nginx-5c6cb7bc9f-4chsb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-fwfcs                       1/1       Running   0          3h
nginx-5c6cb7bc9f-m2hvb                       1/1       Running   0          3h
nginx-5c6cb7bc9f-vd7t2                       1/1       Running   0          3h
redis-master-5d4c55b49d-c7ckn                1/1       Running   0          2h
redis-slave-55d7485bf7-6z4zw                 1/1       Running   24         2h
redis-slave-55d7485bf7-8h7z5                 1/1       Running   24         2h
redis-slave-55d7485bf7-kk627                 1/1       Running   24         2h
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ kubectl get svc
NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
foppish-jackal-redis-master   ClusterIP   10.109.196.105   <none>        6379/TCP         16h
foppish-jackal-redis-slave    ClusterIP   10.110.149.112   <none>        6379/TCP         16h
frontend                      ClusterIP   10.99.195.240    <none>        80/TCP           31s
http                          NodePort    10.99.38.41      <none>        8080:32514/TCP   51m
kubernetes                    ClusterIP   10.96.0.1        <none>        443/TCP          16h
redis-master                  ClusterIP   10.101.38.81     <none>        6379/TCP         32m
redis-slave                   ClusterIP   10.109.190.161   <none>        6379/TCP         32m
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```

```
user@workstation:~/bitnami/intel-training-1/guestbook$ sudo cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       workstation

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.99.100   foo.bar.com
user@workstation:~/bitnami/intel-training-1/guestbook$ echo "$(minikube ip)   guestbook-site.com" | sudo tee -a /etc/hosts
192.168.99.100   guestbook-site.com
user@workstation:~/bitnami/intel-training-1/guestbook$ sudo cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       workstation

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.168.99.100   foo.bar.com
192.168.99.100   guestbook-site.com
user@workstation:~/bitnami/intel-training-1/guestbook$ 
```
