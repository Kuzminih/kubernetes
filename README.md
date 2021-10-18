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
[manifest example](https://github.com/Kuzminih/kubernetes#example-manifest-for-deployment)
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

#### service
[manifest example](https://github.com/Kuzminih/kubernetes#manifes-service)
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
#### Ingress Controller
[list ingress controllers](https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b)
[manifest example](https://github.com/Kuzminih/kubernetes#manifest-for-ingress-rules)
```
#ingress rules
We create rules to route requests between our modules with some resources, such as different web applications located on different pods.

kubectl apply -f (The links can be viewed from the list above) 
kubectl get services -n projectcontour envoy -o wide
after we create some [deployments] with [scale] and [service]  

[manifest for ingress rules]

kubectl apply -f file.yml
kubectl get ingress
kubectl describe ingress

you can run multiple files describing the rules

You can run the reconfiguration online by reusing kubectl apply

kubectl apply -f file.yml
kubectl delete ns projectcontour
```

#### [helm](helm.sh)
create chart.yaml and velues.yaml
chart: основное описание
helm create "name"
exampl:
```
apiVersion: v2
name: App-Helm
description: "desc"
type: application
version: 0.1.0
appVersion: "1.2.3"

keywords:
  - apache
  - http
  -https

maintainers:
  - name: "some name"
  - email: "some email"
  - url: "some url"
```


values: 
```
container:
  image: "some image"
replicacount: 2
```
go to deployment file and service file

```

name: {{ .Release.Name }} #helm deployment


replicas: {{ .Values.replicaCount }}

image: {{ .Values.container.image }}
```
helm list

helm install app(name) "/path/to/cahrt/"

helm list

kubectl get pods
kubectl get deploy
kubectl get service

helm install app1(name) "/path/to/cahrt/"  --set container.image="some else image" #change default variable from variable file

helm chart we can upgrade

helm upgarde  app(name) "/path/to/cahrt/" --set replicaCount=4 --set "some.variable"

kubectl get svc
helm upgarde  app(name) "/path/to/cahrt/" -f "path/to/variable.yml

can run from archive file
helm package /path/to/file
helm install app4 /path/to/file.tgz
can install charts from internet resourse
helm search hub apache

helm install custom-apache bitnami/apache -f balbla.yaml

helm delete "name"
helm un install "name"

## manifest yml exampl
#### for 1 container 
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
#### for 2 containers
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
#### example manifest for deployment
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
#### manifest with replicas
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
#### manifest with autoscaling
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
#### manifes service
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
        project: mossad #service will look for those labels(project)
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
#### manifest for ingress rules
```
apiVersion: networking.k8s.io/(version)
kind: Ingress
metadata:
  name: ingress-hosts

  spec:
    rules:
    - host: www.examle.com
      http:
        path:
          backend:
            serviceName: web #name of service
            servicePort: 80 # service port

    rules:
    - host: www.examle1.com
      http:
        path:
          backend:
            serviceName: web1 #name of service1
            servicePort: 80 # service port
    rules:
    - host: www.examle2.com
      http:
        path:
          backend:
            serviceName: web2 #name of service2
            servicePort: 80 # service port
    
    rules:
    - host: www.examle2.com
      http:
        path:
        - path: "/page2"
          backend:
            serviceName: web3 #name of service3
            servicePort: 80 # service port

        - path: "/page3"
          backend:
            serviceName: web3 #name of service3
            servicePort: 80 # service port    
```
## Troubleshooting

kubectl describe pod
