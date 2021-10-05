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
kubectl rollout history deployment\"name"
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
for 2 containers
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
## Troubleshooting

kubectl describe pod