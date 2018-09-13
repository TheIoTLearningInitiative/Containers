# Cluster Administration Operations

```
#
# Kubernetes Training
#

###################
# Helm Installation
###################

## Linux installation
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.10.0-linux-amd64.tar.gz && tar xfv helm-v2.10.0-darwin-amd64.tar.gz && sudo mv darwin-amd64/helm /usr/local/bin
## OS X installation
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.10.0-darwin-amd64.tar.gz && tar xfv helm-v2.10.0-darwin-amd64.tar.gz && sudo mv darwin-amd64/helm /usr/local/bin

## First steps with Helm
helm init
helm search stable
helm install stable/dokuwiki
helm list
```

```
user@workstation:~$ helm init
$HELM_HOME has been configured at /home/user/.helm.
Warning: Tiller is already installed in the cluster.
(Use --client-only to suppress this message, or --upgrade to upgrade Tiller to the current version.)
Happy Helming!
user@workstation:~$ helm search stable
NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                                 
stable/acs-engine-autoscaler            2.2.0           2.1.1                           Scales worker nodes within agent pools                      
stable/aerospike                        0.1.7           v3.14.1.2                       A Helm chart for Aerospike in Kubernetes                    
stable/anchore-engine                   0.2.1           0.2.4                           Anchore container analysis and policy evaluation engine s...
stable/apm-server                       0.1.0           6.2.4                           The server receives data from the Elastic APM agents and ...
stable/ark                              1.2.1           0.9.1                           A Helm chart for ark
```

```
user@workstation:~$ helm install stable/dokuwiki
NAME:   austere-walrus
LAST DEPLOYED: Thu Sep 13 10:12:33 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                     DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
austere-walrus-dokuwiki  1        1        1           0          1s

==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
austere-walrus-dokuwiki-b78c854d8-hls8q  0/1    Pending  0         1s

==> v1/Secret
NAME                     TYPE    DATA  AGE
austere-walrus-dokuwiki  Opaque  1     3s

==> v1/PersistentVolumeClaim
NAME                              STATUS   VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
austere-walrus-dokuwiki-apache    Pending  standard                                  3s
austere-walrus-dokuwiki-dokuwiki  Bound    pvc-7108de47-b767-11e8-b38d-08002736b0e9  8Gi  RWO  standard  2s

==> v1/Service
NAME                     TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
austere-walrus-dokuwiki  LoadBalancer  10.96.228.3  <pending>    80:32555/TCP,443:32496/TCP  1s


NOTES:

** Please be patient while the chart is being deployed **

1. Get the DokuWiki URL by running:

** Please ensure an external IP is associated to the austere-walrus-dokuwiki service before proceeding **
** Watch the status using: kubectl get svc --namespace default -w austere-walrus-dokuwiki **

  export SERVICE_IP=$(kubectl get svc --namespace default austere-walrus-dokuwiki -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  echo "URL: http://$SERVICE_IP/"

2. Login with the following credentials

  echo Username: user
  echo Password: $(kubectl get secret --namespace default austere-walrus-dokuwiki -o jsonpath="{.data.dokuwiki-password}" | base64 --decode)

user@workstation:~$ 
```

```
user@workstation:~$ helm list
NAME            REVISION        UPDATED                         STATUS          CHART           APP VERSION             NAMESPACE
austere-walrus  1               Thu Sep 13 10:12:33 2018        DEPLOYED        dokuwiki-2.0.6  0.20180422.201805030840 default  
foppish-jackal  1               Tue Sep 11 22:19:38 2018        DEPLOYED        redis-3.10.0    4.0.11                  default  
kubeapps        1               Tue Sep 11 22:22:24 2018        FAILED          kubeapps-0.4.1  v1.0.0-beta.0           kubeapps 
```

## Skeleton

```
## Create a new chart skeleton
helm create my-nginx
## Let's use the solution we made to create a WP chart
mv my-nginx/ my-wordpress/
rm my-wordpress/templates/*
cp -rf ../intel-training-1/activity/solution/*.yaml my-wordpress/templates/
helm install my-wordpress/

## Deploy WordPress modifying some settings
helm install my-wordpress --set app.blogname=”This is my blog”

## Install Official Stable WP and Upgrades
helm install stable/wordpres --name my-stable-wordpress
helm upgrade my-stable-wordpress stable/wordpress --set ingress.enabled=true --set wordpressBlogName="Kubernetes Training"
helm upgrade my-stable-wordpress stable/wordpress --set image.tag=4.10
helm history my-stable-wordpress
```

```
user@workstation:~/bitnami/intel-training-2$ helm create my-nginx
Creating my-nginx
user@workstation:~/bitnami/intel-training-2$ mv my-nginx/ my-wordpress/
user@workstation:~/bitnami/intel-training-2$ tree -L 3 my-wordpress/
my-wordpress/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── ingress.yaml
│   ├── NOTES.txt
│   └── service.yaml
└── values.yaml

2 directories, 7 files
```

```
user@workstation:~/bitnami/intel-training-2$ rm my-wordpress/templates/*
```

```
user@workstation:~/bitnami/intel-training-2$ ls ../intel-training-1/activity/solution/*
../intel-training-1/activity/solution/cm.yaml                  ../intel-training-1/activity/solution/mariadb-svc.yaml
../intel-training-1/activity/solution/commands.bash            ../intel-training-1/activity/solution/wordpress-canary-deployment.yaml
../intel-training-1/activity/solution/ingress.yaml             ../intel-training-1/activity/solution/wordpress-deployment.yaml
../intel-training-1/activity/solution/mariadb-deployment.yaml  ../intel-training-1/activity/solution/wordpress-svc.yaml
../intel-training-1/activity/solution/mariadb-pvc.yaml
```

```
user@workstation:~/bitnami/intel-training-2$ cp -rf ../intel-training-1/activity/solution/*.yaml my-wordpress/templates/
```

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
Error: release broken-dragon failed: namespaces "activity" not found
user@workstation:~/bitnami/intel-training-2$ kubectl create ns myns
namespace/myns created
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
Error: release flabby-heron failed: namespaces "activity" not found
user@workstation:~/bitnami/intel-training-2$ kubectl create ns activity
namespace/activity created
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
Error: release halting-pig failed: persistentvolumeclaims "mariadb-data" already exists
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
Error: release halting-pig failed: persistentvolumeclaims "mariadb-data" already exists
user@workstation:~/bitnami/intel-training-2$ kubectl get pvc
NAME                                       STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
austere-walrus-dokuwiki-apache             Bound     pvc-70f1ffc0-b767-11e8-b38d-08002736b0e9   1Gi        RWO            standard       11m
austere-walrus-dokuwiki-dokuwiki           Bound     pvc-7108de47-b767-11e8-b38d-08002736b0e9   8Gi        RWO            standard       11m
mariadb-data                               Bound     pvc-6f46c756-b6d0-11e8-b38d-08002736b0e9   8Gi        RWO            standard       18h
redis-data-foppish-jackal-redis-master-0   Bound     pvc-ae70e783-b63a-11e8-b38d-08002736b0e9   8Gi        RWO            standard       1d
user@workstation:~/bitnami/intel-training-2$ kubectl delete pvc mariadb-data
persistentvolumeclaim "mariadb-data" deleted
```

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
\Error: release giddy-mule failed: configmaps "wordpress-config" already exists
user@workstation:~/bitnami/intel-training-2$ kubectl get cm
NAME                          DATA      AGE
foppish-jackal-redis-health   3         1d
mysql                         2         19h
user@workstation:~/bitnami/intel-training-2$ kubectl delete cm mysql
configmap "mysql" deleted
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/                                                                                                                  
Error: release independent-toucan failed: configmaps "wordpress-config" already exists
user@workstation:~/bitnami/intel-training-2$ kubectl get cm --all-namespaces
NAMESPACE     NAME                                    DATA      AGE
activity      wordpress-config                        3         16m
default       foppish-jackal-redis-health             3         1d
kube-public   cluster-info                            2         1d
kube-system   austere-walrus.v1                       1         26m
kube-system   broken-dragon.v1                        1         17m
kube-system   extension-apiserver-authentication      6         1d
kube-system   flabby-heron.v1                         1         16m
kube-system   foppish-jackal.v1                       1         1d
kube-system   giddy-mule.v1                           1         1m
kube-system   halting-pig.v1                          1         16m
kube-system   independent-toucan.v1                   1         1m
kube-system   ingress-controller-leader-nginx         0         19h
kube-system   kube-proxy                              2         1d
kube-system   kubeadm-config                          1         1d
kube-system   kubeapps.v1                             1         1d
kube-system   kubernetes-dashboard-settings           1         1d
kube-system   nginx-load-balancer-conf                2         19h
kubeapps      kubeapps-apprepository-jobs-bootstrap   1         1d
kubeapps      kubeapps-dashboard-config               2         1d
kubeapps      kubeapps-frontend-config                1         1d
user@workstation:~/bitnami/intel-training-2$ kubectl delete cm wordpress-config -n activity
configmap "wordpress-config" deleted
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ kubectl get deploy
No resources found.
user@workstation:~/bitnami/intel-training-2$ 
```

Finally

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/                                                                                                                  
NAME:   donating-llama
LAST DEPLOYED: Thu Sep 13 10:44:01 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Ingress
NAME       HOSTS                   ADDRESS  PORTS  AGE
wordpress  wordpress.activity.com  80, 443  0s

==> v1/Pod(related)
NAME                               READY  STATUS             RESTARTS  AGE
mariadb-6686649c4d-jbdqf           0/1    Pending            0         1s
wordpress-canary-79ffdb88d9-c4ppj  0/1    ContainerCreating  0         0s
wordpress-557f8b95c5-gz8lz         0/1    ContainerCreating  0         0s
wordpress-557f8b95c5-wscs8         0/1    ContainerCreating  0         0s

==> v1/ConfigMap
NAME              DATA  AGE
wordpress-config  3     1s

==> v1/PersistentVolumeClaim
NAME          STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
mariadb-data  Bound   pvc-d6394940-b76b-11e8-b38d-08002736b0e9  8Gi       RWO           standard      1s

==> v1/Service
NAME       TYPE       CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
mariadb    ClusterIP  10.109.9.192  <none>       3306/TCP        1s
wordpress  ClusterIP  10.101.31.71  <none>       80/TCP,443/TCP  1s

==> v1beta1/Deployment
NAME              DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
mariadb           1        1        1           0          1s
wordpress-canary  1        1        1           0          1s
wordpress         2        2        2           0          1s
```
