# Drone Software Development

```
user@workstation:~/bitnami/intel-training-1$ helm create copter
Creating copter
```

```
user@workstation:~/bitnami/intel-training-1$ tree -L 3 copter/
copter/
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
user@workstation:~/bitnami/intel-training-1$ 
```

```
user@workstation:~/bitnami/intel-training-1$ nano copter/templates/service.yaml
```

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "copter.fullname" . }}
  labels:
    app: {{ include "copter.name" . }}
    chart: {{ include "copter.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: {{ include "copter.name" . }}
    release: {{ .Release.Name }}
```

```
user@workstation:~/bitnami/intel-training-1$ helm install --dry-run --debug ./copter
[debug] Created tunnel using local port: '37961'

[debug] SERVER: "127.0.0.1:37961"

[debug] Original chart version: ""
[debug] CHART PATH: /home/user/bitnami/intel-training-1/copter

NAME:   exacerbated-greyhound
REVISION: 1
RELEASED: Thu Sep 13 15:11:03 2018
CHART: copter-0.1.0
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
affinity: {}
fullnameOverride: ""
image:
  pullPolicy: IfNotPresent
  repository: nginx
  tag: stable
ingress:
  annotations: {}
  enabled: false
  hosts:
  - chart-example.local
  path: /
  tls: []
nameOverride: ""
nodeSelector: {}
replicaCount: 1
resources: {}
service:
  port: 80
  type: ClusterIP
tolerations: []

HOOKS:
MANIFEST:

---
---
# Source: copter/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: exacerbated-greyhound-copter
  labels:
    app: copter
    chart: copter-0.1.0
    release: exacerbated-greyhound
    heritage: Tiller
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: copter
    release: exacerbated-greyhound
---
# Source: copter/templates/deployment.yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: exacerbated-greyhound-copter
  labels:
    app: copter
    chart: copter-0.1.0
    release: exacerbated-greyhound
    heritage: Tiller
spec:
  replicas: 1
  selector:
    matchLabels:
      app: copter
      release: exacerbated-greyhound
  template:
    metadata:
      labels:
        app: copter
        release: exacerbated-greyhound
    spec:
      containers:
        - name: copter
          image: "nginx:stable"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}
```

```
user@workstation:~/bitnami/intel-training-1$ helm install --dry-run --debug copter --set service.internalPort=8080
```

```
user@workstation:~/bitnami/intel-training-1$ helm install --name example copter --set service.type=NodePort
NAME:   example
LAST DEPLOYED: Thu Sep 13 15:21:08 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME            TYPE      CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
example-copter  NodePort  10.110.8.194  <none>       5763:31220/UDP  0s

==> v1beta2/Deployment
NAME            DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
example-copter  1        0        0           0          0s


NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services example-copter)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT

user@workstation:~/bitnami/intel-training-1$ 
```
