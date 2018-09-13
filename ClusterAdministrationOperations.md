# Cluster Administration Operations

- bitnami.com/stack/gitorious
- https://bitnami.com/stack/gitlab
- https://bitnami.com/stacks

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

Finally, helm install to Tiller

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

```
user@workstation:~/bitnami/intel-training-2$ kubectl get cm --all-namespaces
NAMESPACE     NAME                                    DATA      AGE
activity      wordpress-config                        3         3m
default       foppish-jackal-redis-health             3         1d
kube-public   cluster-info                            2         1d
kube-system   austere-walrus.v1                       1         34m
kube-system   broken-dragon.v1                        1         25m
kube-system   donating-llama.v1                       1         3m
kube-system   extension-apiserver-authentication      6         1d
kube-system   flabby-heron.v1                         1         24m
kube-system   foppish-jackal.v1                       1         1d
kube-system   giddy-mule.v1                           1         10m
kube-system   halting-pig.v1                          1         24m
kube-system   independent-toucan.v1                   1         9m
kube-system   ingress-controller-leader-nginx         0         20h
kube-system   joking-bumblebee.v1                     1         7m
kube-system   kube-proxy                              2         1d
kube-system   kubeadm-config                          1         1d
kube-system   kubeapps.v1                             1         1d
kube-system   kubernetes-dashboard-settings           1         1d
kube-system   nginx-load-balancer-conf                2         20h
kubeapps      kubeapps-apprepository-jobs-bootstrap   1         1d
kubeapps      kubeapps-dashboard-config               2         1d
kubeapps      kubeapps-frontend-config                1         1d
user@workstation:~/bitnami/intel-training-2$ 
```

## Substitution

```
user@workstation:~/bitnami/intel-training-2$ kubectl edit deploy wordpress -n activity
```

```
user@workstation:~/bitnami/intel-training-2$ helm list
NAME                    REVISION        UPDATED                         STATUS          CHART           APP VERSION             NAMESPACE
austere-walrus          1               Thu Sep 13 10:12:33 2018        DEPLOYED        dokuwiki-2.0.6  0.20180422.201805030840 default  
broken-dragon           1               Thu Sep 13 10:21:43 2018        FAILED          my-nginx-0.1.0  1.0                     default  
donating-llama          1               Thu Sep 13 10:44:01 2018        DEPLOYED        my-nginx-0.1.0  1.0                     default  
flabby-heron            1               Thu Sep 13 10:22:29 2018        FAILED          my-nginx-0.1.0  1.0                     default  
foppish-jackal          1               Tue Sep 11 22:19:38 2018        DEPLOYED        redis-3.10.0    4.0.11                  default  
giddy-mule              1               Thu Sep 13 10:37:14 2018        FAILED          my-nginx-0.1.0  1.0                     default  
halting-pig             1               Thu Sep 13 10:22:37 2018        FAILED          my-nginx-0.1.0  1.0                     default  
independent-toucan      1               Thu Sep 13 10:37:47 2018        FAILED          my-nginx-0.1.0  1.0                     default  
joking-bumblebee        1               Thu Sep 13 10:40:10 2018        FAILED          my-nginx-0.1.0  1.0                     default  
kubeapps                1               Tue Sep 11 22:22:24 2018        FAILED          kubeapps-0.4.1  v1.0.0-beta.0           kubeapps 
```

vim

```
user@workstation:~/bitnami/intel-training-2$ vim my-wordpress/templates/wordpress-deployment.yaml 
...
{{- if .Values.app.blogName }} 
value: {{ .Values.app.blogName | quote }}
...
```

```
helm install my-wordpress --set app.blogName="This is my bloggy"
```

```
user@workstation:~/bitnami/intel-training-2$ helm list
NAME                    REVISION        UPDATED                         STATUS          CHART           APP VERSION             NAMESPACE
austere-walrus          1               Thu Sep 13 10:12:33 2018        DEPLOYED        dokuwiki-2.0.6  0.20180422.201805030840 default  
broken-dragon           1               Thu Sep 13 10:21:43 2018        FAILED          my-nginx-0.1.0  1.0                     default  
donating-llama          1               Thu Sep 13 10:44:01 2018        DEPLOYED        my-nginx-0.1.0  1.0                     default  
flabby-heron            1               Thu Sep 13 10:22:29 2018        FAILED          my-nginx-0.1.0  1.0                     default  
foppish-jackal          1               Tue Sep 11 22:19:38 2018        DEPLOYED        redis-3.10.0    4.0.11                  default  
giddy-mule              1               Thu Sep 13 10:37:14 2018        FAILED          my-nginx-0.1.0  1.0                     default  
halting-pig             1               Thu Sep 13 10:22:37 2018        FAILED          my-nginx-0.1.0  1.0                     default  
independent-toucan      1               Thu Sep 13 10:37:47 2018        FAILED          my-nginx-0.1.0  1.0                     default  
joking-bumblebee        1               Thu Sep 13 10:40:10 2018        FAILED          my-nginx-0.1.0  1.0                     default  
kubeapps                1               Tue Sep 11 22:22:24 2018        FAILED          kubeapps-0.4.1  v1.0.0-beta.0           kubeapps 
user@workstation:~/bitnami/intel-training-2$ helm delete --purge
Error: command 'delete' requires a release name
user@workstation:~/bitnami/intel-training-2$ helm delete --purge austere-walrus broken-dragon donating-llama
release "austere-walrus" deleted
release "broken-dragon" deleted
release "donating-llama" deleted
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ helm install my-wordpress/
NAME:   wistful-abalone
LAST DEPLOYED: Thu Sep 13 11:00:06 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME              DATA  AGE
wordpress-config  3     0s

==> v1/PersistentVolumeClaim
NAME          STATUS   VOLUME    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
mariadb-data  Pending  standard  0s

==> v1/Service
NAME       TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)         AGE
mariadb    ClusterIP  10.101.67.186  <none>       3306/TCP        0s
wordpress  ClusterIP  10.97.219.82   <none>       80/TCP,443/TCP  0s

==> v1beta1/Deployment
NAME              DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
mariadb           1        1        1           0          0s
wordpress-canary  1        1        1           0          0s
wordpress         2        2        2           0          0s

==> v1beta1/Ingress
NAME       HOSTS                   ADDRESS  PORTS  AGE
wordpress  wordpress.activity.com  80, 443  0s

==> v1/Pod(related)
NAME                               READY  STATUS   RESTARTS  AGE
mariadb-6686649c4d-7z8k7           0/1    Pending  0         0s
wordpress-canary-79ffdb88d9-zm97p  0/1    Pending  0         0s
wordpress-557f8b95c5-8hnxb         0/1    Pending  0         0s
wordpress-557f8b95c5-tppmx         0/1    Pending  0         0s
```

```
user@workstation:~/bitnami/intel-training-2$ helm list
NAME            REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE
kubeapps        1               Tue Sep 11 22:22:24 2018        FAILED          kubeapps-0.4.1  v1.0.0-beta.0   kubeapps 
wistful-abalone 1               Thu Sep 13 11:00:06 2018        DEPLOYED        my-nginx-0.1.0  1.0             default  
user@workstation:~/bitnami/intel-training-2$ 
```

## Dependencies

https://github.com/helm/charts

```

```

## Home Assistant

```
user@workstation:~/bitnami/intel-training-2$ helm install stable/home-assistant
NAME:   invited-fly
LAST DEPLOYED: Thu Sep 13 11:05:59 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME                                     TYPE    DATA  AGE
invited-fly-home-assistant-configurator  Opaque  0     0s

==> v1/PersistentVolumeClaim
NAME                        STATUS   VOLUME    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
invited-fly-home-assistant  Pending  standard  0s

==> v1/Service
NAME                        TYPE       CLUSTER-IP      EXTERNAL-IP  PORT(S)   AGE
invited-fly-home-assistant  ClusterIP  10.106.229.131  <none>       8123/TCP  0s

==> v1beta2/Deployment
NAME                        DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
invited-fly-home-assistant  1        1        1           0          0s

==> v1/Pod(related)
NAME                                         READY  STATUS   RESTARTS  AGE
invited-fly-home-assistant-5f54d6b58f-xrsch  0/1    Pending  0         0s


NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=home-assistant,release=invited-fly" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl port-forward $POD_NAME 8080:80

user@workstation:~/bitnami/intel-training-2$ helm list
NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
invited-fly     1               Thu Sep 13 11:05:59 2018        DEPLOYED        home-assistant-0.1.45   0.74.2          default  
kubeapps        1               Tue Sep 11 22:22:24 2018        FAILED          kubeapps-0.4.1          v1.0.0-beta.0   kubeapps 
triangular-zebu 1               Thu Sep 13 11:04:10 2018        DEPLOYED        home-assistant-0.1.45   0.74.2          default  
wistful-abalone 1               Thu Sep 13 11:00:06 2018        DEPLOYED        my-nginx-0.1.0          1.0             default  
user@workstation:~/bitnami/intel-training-2$   export POD_NAME=$(kubectl get pods --namespace default -l "app=home-assistant,release=invited-fly" -o jsonpath="{.items[0].metadata.name}")
```

```
user@workstation:~/bitnami/intel-training-2$   echo "Visit http://127.0.0.1:8080 to use your application"
Visit http://127.0.0.1:8080 to use your application
user@workstation:~/bitnami/intel-training-2$   kubectl port-forward $POD_NAME 8080:80
error: unable to forward port because pod is not running. Current status=Pending
user@workstation:~/bitnami/intel-training-2$ echo $POD_NAME
invited-fly-home-assistant-5f54d6b58f-xrsch
user@workstation:~/bitnami/intel-training-2$ kubectl port-forward $POD_NAME 8080:80
error: unable to forward port because pod is not running. Current status=Pending
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ kubectl get pods
NAME                                              READY     STATUS              RESTARTS   AGE
invited-fly-home-assistant-5f54d6b58f-xrsch       0/1       ContainerCreating   0          2m
triangular-zebu-home-assistant-7bc4c9f9df-q4lkf   0/1       ContainerCreating   0          3m
```

##

```
user@workstation:~/bitnami/intel-training-2$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Unable to get an update from the "stable" chart repository (https://kubernetes-charts.storage.googleapis.com):
        read tcp 10.215.56.130:45862->216.58.194.176:443: read: connection reset by peer
...Unable to get an update from the "bitnami" chart repository (https://charts.bitnami.com/bitnami):
        read tcp 10.215.56.130:46836->13.35.121.13:443: read: connection reset by peer
Update Complete. ⎈ Happy Helming!⎈ 
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ helm install stable/wordpress --name my-stable-wordpress
user@workstation:~/bitnami/intel-training-2$ helm upgrade my-stable-wordpress stable/wordpress --set ingress.enabled=true --set wordpressBlogName="Kubernetes Training"
user@workstation:~/bitnami/intel-training-2$ helm upgrade my-stable-wordpress stable/wordpress --set image.tag=4.10
...
...
NOTES:
1. Get the WordPress URL:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w my-stable-wordpress-wordpress'

  export SERVICE_IP=$(kubectl get svc --namespace default my-stable-wordpress-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  echo "WordPress URL: http://$SERVICE_IP/"
  echo "WordPress Admin URL: http://$SERVICE_IP/admin"

2. Login with the following credentials to see your blog

  echo Username: user
  echo Password: $(kubectl get secret --namespace default my-stable-wordpress-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)

...
...
```

```
user@workstation:~/bitnami/intel-training-2$ kubectl get svc --namespace default -w my-stable-wordpress-wordpress
NAME                            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
my-stable-wordpress-wordpress   LoadBalancer   10.104.143.25   <pending>     80:31131/TCP,443:32129/TCP   4m
^Cuser@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ kubectl get svc --namespace default -w my-stable-wordpress-wordpress
NAME                            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
my-stable-wordpress-wordpress   LoadBalancer   10.104.143.25   <pending>     80:31131/TCP,443:32129/TCP   4m
^Cuser@workstation:~/bitnami/intel-training-2$ export SERVICE_IP=$(kubectl get svc --namespace default my-stable-wordpress-wordpress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
user@workstation:~/bitnami/intel-training-2$ echo "WordPress URL: http://$SERVICE_IP/"
WordPress URL: http:///
user@workstation:~/bitnami/intel-training-2$ echo $SERVICE_IP

user@workstation:~/bitnami/intel-training-2$ kubectl get svc --namespace default my-stable-wordpress-wordpress
NAME                            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
my-stable-wordpress-wordpress   LoadBalancer   10.104.143.25   <pending>     80:31131/TCP,443:32129/TCP   5m
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ helm history my-stable-wordpress
REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION     
1               Thu Sep 13 11:12:53 2018        SUPERSEDED      wordpress-2.1.10        Install complete
2               Thu Sep 13 11:13:05 2018        SUPERSEDED      wordpress-2.1.10        Upgrade complete
3               Thu Sep 13 11:13:15 2018        DEPLOYED        wordpress-2.1.10        Upgrade complete
user@workstation:~/bitnami/intel-training-2$ 
```

```
user@workstation:~/bitnami/intel-training-2$ sudo nano /etc/hosts
```

```
192.168.99.100   wordpress.local   
```

Go to 

## Helm Repo

```
## Chart repositories
helm repo list
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

```
user@workstation:~/bitnami/intel-training-2$ helm repo list
NAME    URL                                             
stable  https://kubernetes-charts.storage.googleapis.com
local   http://127.0.0.1:8879/charts                    
bitnami https://charts.bitnami.com/bitnami              
user@workstation:~/bitnami/intel-training-2$ helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
user@workstation:~/bitnami/intel-training-2$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "bitnami" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈ 
user@workstation:~/bitnami/intel-training-2$ 
```

## Deploy WordPress modifying some settings

```
## Deploy WordPress modifying some settings
helm install my-wordpress --set app.blogname=”This is my blog”

## Install Official Stable WP and Upgrades
helm install stable/wordpress --name my-stable-wordpress
helm upgrade my-stable-wordpress stable/wordpress --set ingress.enabled=true --set wordpressBlogName="Kubernetes Training"
helm upgrade my-stable-wordpress stable/wordpress --set image.tag=4.10
helm history my-stable-wordpress
```

## Jsonnet

```
#####################
# Jsonnet Installation
#####################

## OS X installation
brew install jsonnet
## Linux Installation - Compile from Source Code
wget https://github.com/google/jsonnet/archive/v0.11.2.tar.gz
## Follow instructions detailed at: https://github.com/google/jsonnet#makefile

## Render jsonnet modification
jsonnet resources/new-frontend.jsonnet
jsonnet resources/new-frontend-2.jsonnet
```

```
user@workstation:~/bitnami/intel-training-2$ wget https://github.com/google/jsonnet/archive/v0.11.2.tar.gz
--2018-09-13 11:44:32--  https://github.com/google/jsonnet/archive/v0.11.2.tar.gz
Resolving github.com (github.com)... 192.30.255.112, 192.30.255.113
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/google/jsonnet/tar.gz/v0.11.2 [following]
--2018-09-13 11:44:34--  https://codeload.github.com/google/jsonnet/tar.gz/v0.11.2
Resolving codeload.github.com (codeload.github.com)... 192.30.255.121, 192.30.255.120
Connecting to codeload.github.com (codeload.github.com)|192.30.255.121|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/x-gzip]
Saving to: ‘v0.11.2.tar.gz’

```

```

```

## Kubecfg

https://github.com/anguslees/kubecfg


## KubeApps

https://hub.kubeapps.com/

```
user@workstation:~$ helm install bitnami/kubeapps
...
Kubeapps can be accessed via port 80 on the following DNS name from within your cluster:

   operatic-swan-kubeapps.default.svc.cluster.local

To access Kubeapps from outside your K8s cluster, follow the steps below:

1. Get the Kubeapps URL by running these commands:

   echo "Kubeapps URL: http://127.0.0.1:8080"
   export POD_NAME=$(kubectl get pods --namespace default -l "app=operatic-swan-kubeapps" -o name)
   kubectl port-forward --namespace default $POD_NAME 8080:8080

2. Open a browser and access Kubeapps using the obtained URL.
```

# RabbitMQ

```
user@workstation:~$ export POD_NAME=$(kubectl get pods --namespace default -l "app=rabbitmq-ha" -o jsonpath="{.items[0].metadata.name}")
...
...
user@workstation:~$ kubectl port-forward $POD_NAME --namespace default 5672:5672 15672:15672
Forwarding from 127.0.0.1:5672 -> 5672
Forwarding from [::1]:5672 -> 5672
Forwarding from 127.0.0.1:15672 -> 15672
Forwarding from [::1]:15672 -> 15672
...
...
NOTES:
** Please be patient while the chart is being deployed **

  Credentials:

    Username      : guest
    Password      : $(kubectl get secret --namespace default idle-serval-rabbitmq-ha -o jsonpath="{.data.rabbitmq-password}" | base64 --decode)
    ErLang Cookie : $(kubectl get secret --namespace default idle-serval-rabbitmq-ha -o jsonpath="{.data.rabbitmq-erlang-cookie}" | base64 --decode)
    

  RabbitMQ can be accessed within the cluster on port 5672 at idle-serval-rabbitmq-ha.default.svc.cluster.local

  To access the cluster externally execute the following commands:

    export POD_NAME=$(kubectl get pods --namespace default -l "app=rabbitmq-ha" -o jsonpath="{.items[0].metadata.name}")
    kubectl port-forward $POD_NAME --namespace default 5672:5672 15672:15672

  To Access the RabbitMQ AMQP port:

    amqp://127.0.0.1:5672/ 

  To Access the RabbitMQ Management interface:

    URL : http://127.0.0.1:15672
```

## Creating Users

- https://github.com/bitnami-labs/k8s-training-resources

```
user@workstation:~/bitnami$ kubectl get clusterrolebindings
NAME                                                   AGE
cluster-admin                                          1d
kubeadm:kubelet-bootstrap                              1d
kubeadm:node-autoapprove-bootstrap                     1d
kubeadm:node-autoapprove-certificate-rotation          1d
kubeadm:node-proxier                                   1d
kubeapps-operator                                      1d
minikube-rbac                                          1d
storage-provisioner                                    1d
system:aws-cloud-provider                              1d
system:basic-user                                      1d
system:controller:attachdetach-controller              1d
system:controller:certificate-controller               1d
system:controller:clusterrole-aggregation-controller   1d
system:controller:cronjob-controller                   1d
system:controller:daemon-set-controller                1d
system:controller:deployment-controller                1d
system:controller:disruption-controller                1d
system:controller:endpoint-controller                  1d
system:controller:generic-garbage-collector            1d
system:controller:horizontal-pod-autoscaler            1d
system:controller:job-controller                       1d
system:controller:namespace-controller                 1d
system:controller:node-controller                      1d
system:controller:persistent-volume-binder             1d
system:controller:pod-garbage-collector                1d
system:controller:pv-protection-controller             1d
system:controller:pvc-protection-controller            1d
system:controller:replicaset-controller                1d
system:controller:replication-controller               1d
system:controller:resourcequota-controller             1d
system:controller:route-controller                     1d
system:controller:service-account-controller           1d
system:controller:service-controller                   1d
system:controller:statefulset-controller               1d
system:controller:ttl-controller                       1d
system:discovery                                       1d
system:kube-controller-manager                         1d
system:kube-dns                                        1d
system:kube-scheduler                                  1d
system:node                                            1d
system:node-proxier                                    1d
system:volume-scheduler                                1d
user@workstation:~/bitnami$ 
```
