# kubernetes
Lecture basic
## Basic
### Worker node - just worker
- kubelet
- kube-prox

### Master node - managment containers on worker nodes 
- kube-apiserver
- kube-control-manager
- kube-scheduler
- etcd

Kubernetes cluster (1-2 master node, 1-more worker)
---
### Containers
- DockerHub
- AWS containerregistry
- Google container registry
- Azure container registry\

### Why kubernetes
- Service discover load balanceer
    dns ip port
- Storage orchestration
    attached network disk with images and containers
- Automated rollouts and rollback
    Live update containers
- Automated bin packing
    balancing resource nodes and copys nodes
- Self-healing
- Secret and configuration managment
    like vault

### Certifications
- CKA
- CKAD

## Example for test using 
Install:
- Virtual box
- Kubectl
- Minicube\
Commands\
minikube start # for starting kube cluster :)\
location config file for minikube cat ~/.kube/config\
~/.minikube/\
kubectl get componentstatuses\
kubectl cluster-info\
kubectl get node\
minikube stop\
minikube delete\
minikube --cpus=1 --memory=2G --disk-size=25G\
minikube ssh

### just commands
#### basic
```
kubectl get nodes
kubectl get pods
kubectl run "name" --generator=run-pod/v1 --image=httpd:latest --port=80
kubectl get pods
kubectl delete pods "name"
kubectl describe pods hello
kubectl exec -it name sh
kubectl logs name
kubectl port forward 7788:80 
kubectl apply -f file-name.yml
kubectl delete -f file-name.yml  ?? reload
```
#### Deployment
```
kubectl create deployment "name" --image httpd:latest
kubectl get deploy
kubectl get deployment "name"
kubectl describe deployment "name"

kubectl scale deployment "name" --replicas 4 #create replics for pods
kubectl get rs  #replica set

kubectl autoscale deployment "name" --min=4 --max=6 --cpu-percent=80
kubectl get hpa

kubectl rollout history deployment\"name"
kubectl rollout status deployment\"name"

change image in deployment
kubectl describe deployment "name"
"containername" from describe
###
containers:
  containername
###
kubectl set image deployment/"name" "containername"="new image" --record
kubectl rollout history deployment/"name"
kubectl rollout undo deployment/"name"
kubectl rollout undo deployment/"name" --to-revision=4
# number of the revision can see from stdout 'kubectl rolluot history deployment/"name"'
kubectl rollout status deployment/"name"
kubectl describe deployment "name"

kubectl roolout restart deployment/"name"
kubectl rollout status deployment/"name"

## run deployment manifest example
kubectl apply -f name.yml


```

### service
```
kubectl create deployment "name" --image httpd:latest
kubectl scale deployment "name" --replicas 4

kubectl expose deployment "name" --type=ClusterIP --port 80

kubectl get services/svc

kubectl delete service "name"

kubectl get services

kubectl expose deployment "name" --type=NodePort --port 80

kubectl get svc

kubectl describe nodes

### loadbalancer in cloud

kubectl expose deployment "name" --type=LoadBalancer --port 80
kubectl get svc

kubectl apply -f name.yml
kubectl get svc


```
### manifest yml exampl

```
apiVersion: v1
kind: Pod
mettadata:
  name: my-web
  labels:
    env: prod
    app: main
    tier: front
    owner: name
spec:
  containers:
    - name: container-httpd
      image: httpd:latest # can change live kubectl apply
      ports:
        containerPort: 80
        hostPort:80
```
### for 2 containers
```
apiVersion: v1
kind: Pod
mettadata:
  name: my-web
  labels:
    env: prod
    app: main
    tier: front
    owner: name
spec:
  containers:
    - name: container-httpd
      image: httpd:latest # can change live kubectl apply
      ports:
        containerPort: 80
        
  
  containers:
    - name: container-app
      image: tomcat:8.5
      ports:
        containerPort: 8080
        
```
### example manifest for deployment
```
apiVersion: apps/v1
kind: deployment
metadata:
  name: my-name-deployment
  labels:
    app: my-k8s-application
spec:
  selector:
    matchLabels:
      project: kgb
  
  template:
    metadata:
      labels:
        project: kgb
    spec:
      containers:
        - name: kgb-web
          image: httpd:latest
          ports:
            containerPort: 80
```
### manifest with replicas
```
apiVersion: apps/v1
kind: deployment
metadata:
  name: my-name-deployment
  labels:
    env: my-app
    app: my-k8s-application
spec:
  replicas: 3
  selector:
    matchLabels:
      project: cia
  
  template:
    metadata:
      labels:
        project: cia
    spec:
      containers:
        - name: cia-web
          image: httpd:latest
          ports:
            containerPort: 80
```
### manifest with autoscaling
```
apiVersion: apps/v1
kind: deployment
metadata:
  name: my-name-deployment
  labels:
    env: my-app
    app: my-k8s-application
spec:
  replicas: 2
  selector:
    matchLabels:
      project: mossad
  
  template:
    metadata:
      labels:
        project: mossad
    spec:
      containers:
        - name: mossad-web
          image: httpd:latest
          ports:
            containerPort: 80
---
apiVersion: autoscaling/v2beta
kind: HorizontalPodAutoscaling
matadata:
  name: myautoscal
spec:
  scaleTargetRef:
    apiVersion: apps/v2
    kind: Deployment
    name: my-name-deployment # должно совпадать с матадатой, именем
  minReplicas: 3
  maxReplicas: 5
  metrics:
    - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 70
    - type: Resource
    resource:
      name: memory
      targetAverageUtilization: 80


```
### manifes service
```
apiVersion: apps/v1
kind: deployment
metadata:
  name: my-name-deployment
  labels:
    env: my-app
    app: my-k8s-application
spec:
  replicas: 2
  selector:
    matchLabels:
      project: mossad
  
  template:
    metadata:
      labels:
        project: mossad #service will look for those labels
    spec:
      containers:
        - name: mossad-web
          image: httpd:latest
          ports:
            containerPort: 80
---
apiVersion: v1
kind: Service
matadata:
  name: service1
  labels:
    env: prod
    app: appv1
    owner:
spec:
  selector:
    project: mossad #selecting pods with those labels
  ports:
    - name: app-listener
      protocol: tcp
      port: 80 #port on LB
      targetport: 80 #port on pod
    - name: app-listener2
      protocol: tcp
      port: 8081 #port on LB
      targetport: 8080 #port on pod
  typy: LoadBalancer
```


## Troubleshooting

kubectl describe pod