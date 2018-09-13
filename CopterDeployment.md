```
user@workstation:~/bitnami/intel-training-1$ nano copter-deployment/copter.yaml
```

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: copter
  labels:
    app: copter
    tier: copter
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: copter
        tier: copter
    spec:
      containers:
      - name: copter
        image: xe1gyq/copter
        ports:
          - name: udp
            containerPort: 5763
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f copter-deployment/copter.yaml 
deployment.extensions/copter created
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -l app=copter,tier=copter
NAME                     READY     STATUS              RESTARTS   AGE
copter-d97b675c6-bmp8t   0/1       ContainerCreating   0          1m
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -l app=copter,tier=copter
NAME                     READY     STATUS    RESTARTS   AGE
copter-d97b675c6-bmp8t   1/1       Running   0          5m
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl port-forward $(kubectl get pods -l app=copter,tier=copter -o jsonpath="{.items[0].metadata.name}") 5763:5763
Forwarding from 127.0.0.1:5763 -> 5763
Forwarding from [::1]:5763 -> 5763
```

## Adding main.sh

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: copter
  labels:
    app: copter
    tier: copter
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: copter
        tier: copter
    spec:
      containers:
      - name: copter
        image: xe1gyq/copter
        command: ["/home/user/main.sh"]
        ports:
        - name: udp
          containerPort: 5763
          protocol: UDP
        volumeMounts:
        - name: main
          mountPath: /home/user/
      volumes:
      - name: main
        configMap:
          name: main
          defaultMode: 0744
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create configmap main --from-file=copter-deployment/main.sh
configmap/main created
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl get cm
NAME                         DATA      AGE
main                         1         3m
my-release-rabbitmq-config   2         2h
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl create -f copter-deployment/copter.yaml
deployment.extensions/copter created
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -l app=copter,tier=copter
NAME                     READY     STATUS              RESTARTS   AGE
copter-cdf864588-cvtxr   0/1       ContainerCreating   0          8s
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl logs copter-cdf864588-cvtxr -f
Error from server (BadRequest): container "copter" in pod "copter-cdf864588-cvtxr" is waiting to start: ContainerCreating
```

```
user@workstation:~/bitnami/intel-training-1$ kubectl expose pod copter-6679675885-2sb8l --type=NodePort --name=copter
user@workstation:~/bitnami/intel-training-1$ kubectl get pods -o wide
user@workstation:~/bitnami/intel-training-1$ kubectl get svc
```
