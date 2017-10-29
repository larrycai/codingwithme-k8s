name: inverse
layout: true
class: center, middle, inverse
---
# Learn Kubernetes in 90 minutes
[codingwithme]
.footnote[author: [larry.cai](mailto:larry.caiyu@gmail.com), slides: [remark](https://github.com/gnab/remark)]

---
layout: false
# Agenda
.left-column[
![](https://kubernetes.io/images/nav_logo2.svg)
]
.right-column[
### Exercised based training to understand basic concepts of **kubernetes**

- Exercise 1: First web service in kubernetes

- Exercise 2: Revisit pod, deployment and service

- Exercise 3: Controller – Deployment (scale)

- Exercise 4: Deploy with YAML file

- Exercise 5: install Microservice: Guestbook 

- Summary & Reference 
]
???
Minikube environment setup is in included, see 

---
# Environment
.left-column[
## k8s-playground
![](image/k8s-playground.png)
### training env
![](image/k8s-arch1.png)
]
.right-column[
Use Google's http://labs.play-with-k8s.com

New 4 instance as four nodes in  (master + 3 workers)
* Node 1 as master node: 
 * follow instruction step 1/2/3
* Node 2,3,4 as worker node
```bash
kubeadm join –token … # check the console log in Node 1
```
- Click the port to open dashboard
- Check inside master node (Node 1)
```bash
kubectl get nodes
```

> Node 1/Step3 dashboard may have problem, check [codingwithme-k8s](https://github.com/larrycai/codingwithme-k8s)

.footnote[.red[*] Use `Ctrl+Ins` and `Shift+Ins` for copy/paste the command in the console]
]
---
# Container
.left-column[
## vs VirtualMachine
.footnote[.red[*] Learn more in <br/>&nbsp;&nbsp;&nbsp;[CodingWithMe Docker]( https://www.slideshare.net/larrycai/learn-docker-in-90-minutes)
]
]
.right-column[
Container technology offers an alternative method for virtualization in cloud, with more efficiency & fast
- New era for packaging and delivering software
- Docker is one execution engine for container

![](image/container-vm-1.png) ![](image/container-vm-2.png)

.pull-left[
```bash
docker pull nginx
docker run -d -p 8080:80 nginx 
```
]
.pull-right[
```bash
docker build -t larrycai/whoami .
docker push larrycai/whoami
```
]

]
---
# Kubernetes
.left-column[
## Platform
]
.right-column[
Kubernetes is an open source .red[container orchestration platform] that helps manage distributed, containerized applications at massive scale.

Kubernetes (k8s) comes from google

kubernetes is a product platform to run container (Docker is one type of container execution engine)
- k8s to container likes openstack to virtual machine.

Key features (list partly):
* Auto-scaling
* Self-healing infrastructure
* Application lifecycle management
* ..
]
---
# Kubernetes
.left-column[
## Platform
## Architecture
]
.right-column[
Kubernete cluster includes Master (control) and Worker (computer) Nodes (multi or single)

Container is executed in Worker Node as default

![](image/k8s-arch.png)

kubernetes service is provided via **REST API** and its plugins
- like Dashboard is plugin using kubernetes REST API
- `kubectl` command communicates with REST API
]

---
# Exercise 1
.left-column[
## First web service
![](image/exer1.png)
]
.right-column[
Let’s start one web server in k8s (cloud), Nginx is webserver like apache

- Start the service from official docker image https://hub.docker.com/_/nginx/ 
```bash
kubectl run nginx --image=nginx --port=80
kubectl expose deployment nginx --type=NodePort
```
- Check what happens & Check dashboard
- Access the nginx web service (click new port)
```bash
kubectl get pods
kubectl get pods –o wide # check node
kubectl get all
kubectl describe nodes
docker ps # in different node
```
- Kill the pod ! And check again
```bash
kubectl delete pods nginx-<xxx>
```
]

---
# Kubernetes
.right-column[
![](image/service-overall.png)

.footnote[.red[*] Image source https://kubernetes.io/images/hellonode/image_13.png 
]
]
---
# Kubernetes
.left-column[
## Pod
![](image/overall-pod.png)
]
.right-column[
A .red[**pod**] is a group of one or more containers (such as Docker containers), the shared storage for those containers

Minimal element in kubernetes, run in one Node

![](image/pod.png)

`kubectl` `pod` related command 
```bash
kubectl run pods
kubectl get pods
kubectl delete pods 
kubectl describe pods <pod>
kubectl exec -it <pod> -- bash 
```
]
---
# Kubernetes
.left-column[
## Pod
## Deployment
![](image/overall-deployment.png)
]
.right-column[
The .red[**Deployment**] is responsible for creating and updating instances of your application (using controller)

Once the application instances are created, a Kubernetes Deployment Controller continuously monitors those instances

Default is one instance and keep active

Review the command 
```bash
kubectl run nginx --image=nginx --port=80 --replicas=1
```
- Download docker image `nginx` (from `hub.docker.com`) as internal port is `80`
- Run docker image inside pod named `nginx-xxx` with 1 instance 
- Deployment with name `nginx`
- If pod is deleted, deployment controller create new one
]
---
# Kubernetes
.left-column[
## Pod
## Deployment
## Service
![](image/overall-service.png)
]
.right-column[
.red[**Service**] is an abstraction which defines a logical set of Pods and a policy by which to access them 
- sometimes called a micro-service
- Service could be selected by label (skipped here)

.red[**Service type**] defines how to expose a Service, The default is `ClusterIP` (internal)
- NodePort : expose port in Kubernetes Master
```
kubectl expose deployment xxx –-type=NodePort
```
- Load Balancer (need support in k8s infra)
- ...
]
---
# Exercise 2
.left-column[
## revisit pod, deployment, service
]
.right-column[
Enter into container inside pod
```bash
kubectl exec -it nginx-xxx -- bash
```
Start pod only without deployment (.red[`--restart=Never`])
```bash
kubectl run nginx --image=nginx --port=80 --restart=Never
kubectl run -i --tty ubuntu --image=ubuntu --restart=Never -- sh
```
Expose existing service to another service with (.red[`--type=NodePort`])
```bash
kubectl expose --name nginx2 deploy nginx
kubectl get service
curl <cluster ip>
kubectl expose --name nginx3 deploy nginx --type=NodePort
```
Clean up the deployment and service
```bash
kubectl delete service xxx
kubectl delete deployment  xxx # check pod removed or not
```
]
---
# Deployment more
.left-column[
## Scale/Replicas
]
.right-column[
Deployment describe the .red[desired state] in a Deployment object, and the Deployment controller will change the actual state to the desired state at a controlled rate for you

Scale to wanted size (.red[--replicas=xx])

```bash
$ kubectl scale --replicas=2 deployment/nginx
$ kubectl scale --replicas=10 deployment/nginx
deployment "nginx" scaled
$ kubectl rollout status deployment/nginx
Waiting for rollout to finish: 2 of 10 updated replicas are available...
…
Waiting for rollout to finish: 9 of 10 updated replicas are available...
deployment "nginx" successfully rolled out
$ kubectl get pods 
```

.red[Patch, Upgrade, Rollback] ..

Autoscale : grow when needed

]
---
# Kubernetes Deployment example
.left-column[
## Canary release
### vs. Blue/Green
]
.right-column[
Kubernetes support to define own deploy strategy. 
- User takes care of the service and what it wants to expose
- The k8s platform do the rest

![](image/canary-release.png)

.footnote[.red[*] source http://blog.kubernetes.io/2017/04/multi-stage-canary-deployments-with-kubernetes-in-the-cloud-onprem.html
]
]
---
# Exercise 3
.left-column[
## deployment with scale
]
.right-column[
Show hostname ( image: `larrycai/whoami`  [@docker hub](https://hub.docker.com/r/larrycai/whoami/))
```bash
kubectl run whoami --image=larrycai/whoami --port=5000
kubectl expose deploy whoami --type=NodePort
```
- Check the hostname in webpage (printout the hostname in the app)
![](image/whoami.png)

Delete `kubectl delete pods whoami-xxxx`
- Check the hostname in webpage (reload) 

Scale In/Scale Out
```bash
kubectl scale --replicas=20 deployment/whoami
kubectl rollout status deployment/whoami
kubectl get pods –o wide
kubectl scale --replicas=2 deployment/whoami
```
- Check the hostname in webpage (reload) 

]
---
# YAML descriptor
.right-column[
`kubectl` command line is limited to set properties of the objects
- Difficult to maintain and version control

.red[YAML file] is used to manage the object (local or .red[web])
```bash
kubectl create -f node-pod.yaml
kubectl create –f http://example.com/nginx-pod.yaml
```
Get full descriptions of the object in YAML 
```bash
$ kubectl get pods nginx2 -o yaml
apiVersion: v1
kind: Pod
metadata:
  name: gluster-pod1
spec:
  containers:
  - name: gluster-pod1
    image: gcr.io/google_containers/nginx-slim:0.8
    ports:
    - name: web
      containerPort: 80
```
]
---
# Exercise 4
.left-column[
## YAML file
]
.right-column[
Create whoami deploy yaml file (use pod as reference)

```bash
kubectl get deploy whoami -o yaml
```
Download and create https://github.com/larrycai/codingwithme-k8s/blob/master/whoami.yaml 
```bash
curl -L -o whoami.yaml https://git.io/v7yd8
kubectl delete service whoami
kubectl delete deploy whoami
kubectl create -f whoami.yaml
```
Change the `ReplicaSet` to 5 and run it again 
```bash
kubectl apply -f whoami.yaml
```
]
---
# Microservice in kubernetes
.left-column[
## Example
### - Guestbook service
]
.right-column[
Kubernetes is a great tool for microservices clustering and orchestration. 

Kubernetes provides lots of features to be used to deploy microservices
- Declare in YAML to deploy them

Kubernetes official [tutorial Guestbook](
https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)

![](image/guestbook-kubernetes.png)
.footnote[.red[*] image source 
https://netmark.jp/wp-content/uploads/2014/12/guestbook-kubernetes.png
]
]
---
# Exercise 5
.left-column[
## Guestbook service
![](image/guestbook.png)
]
.right-column[
Let's try to deploy all in one 
![](image/guestbook-kubernetes.png)
```bash
kubectl create -f https://git.io/v7ytR # shorturl to guestbook-all-in-one.yaml
```
- Check dashboards 

Expose service to NodePort
```bash
kubectl expose svc frontend --name f2 --type=NodePort
```
- Check the guestbook service
]
---
# Summary
.right-column[
## Kubernetes: a .red[**orchestration platform**] to run **container** (docker)

## Concept covered here:
- A **Node** is a master/worker machine in Kubernetes

- A **Pod** is the basic building block of Kubernetes, which has a group of containers

- **Deployment** controls the state of the pod (scale, replicate)

- **Service** is an abstraction which defines a logical set of Pods and a policy by which to access them. (micro service ..)

## Kubernetes grows very fast, follow it.
]
---
# Reference
.left-column[
![](image/book-kubernetes-in-action.png)
]
.right-column[
* K8s doc: https://kubernetes.io/docs/home/
* Code: https://github.com/larrycai/codingwithme-k8s 
* Minikube: https://github.com/kubernetes/minikube 
* Refcard: https://dzone.com/refcardz/kubernetes-essentials 
* Blog: multi stage deployment with kubernetes: http://blog.kubernetes.io/2017/04/multi-stage-canary-deployments-with-kubernetes-in-the-cloud-onprem.html
* Video: [The Illustrated Children's Guide to Kubernetes](https://www.youtube.com/watch?v=4ht22ReBjno)
* Sandbox online: http://labs.play-with-k8s.com/ 
* Book: [Kubernetes in Action (Manning)](https://www.manning.com/books/kubernetes-in-action)
]

---
# Changelog
.right-column[
* 2017/07/23: first version 
* 2017/08/11: use k8s playground
* 2017/10/2: fix dashboard url
* 2017/10/16: minor changes in Ex2
* 2017/10/26: link to README.md 
* 2017/10/29: change to markdown based
]
---
name: last-page
template: inverse

## That's all folks (for now)!

created using [remark](http://github.com/gnab/remark).

.footnote[fork me @ [github](https://github.com/larrycai/codingwithme-k8s)]

