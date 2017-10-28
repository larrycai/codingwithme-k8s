name: inverse
layout: true
class: center, middle, inverse
---
# Learn Kubernetes in 90 minutes
[codingwithme]
.footnote[author: [larry.cai](mailto:larry.caiyu@gmail.com), slides: [remark](https://github.com/gnab/remark)]

---
layout: false
.left-column[
## Agenda
![](https://kubernetes.io/images/nav_logo2.svg)
]
.right-column[
- Introduction

- Exercise 1: First web service in kubernetes

- Exercise 2: Revisit pod, deployment and service

- Exercise 3: Controller – Deployment (scale)

- Exercise 4: Deploy with YAML file

- Exercise 5: install Microservice: Guestbook 

- Reference 

.footnote[.red[*] Minikube environment setup is in appendix]
]
---
.left-column[
## Environment
]
.right-column[
Four nodes in http://labs.play-with-k8s.com (master + 3 workers)

- Node 1 as master node: follow instruction step 1/2/3
##### .right[Step3 may have problem, check [codingwithme-k8s](https://github.com/larrycai/codingwithme-k8s)]
- Node 2,3,4 as worker node

`kubeadm join –token … # check the console log in Node 1`

- Click the port to open dashboard
- Check inside master node (Node 1)

`kubectl get nodes`

.footnote[.red[*] Use `Ctrl+Ins` and `Shift+Ins` for copy/paste the command]
]
---
.left-column[
## Container
]
.right-column[
Container technology offers an alternative method for virtualization in cloud, with more efficiency & fast
- New era for packaging and delivering software
- Docker is one execution engine for container

<img src="http://blog.jayway.com/wp-content/uploads/2015/03/vm-vs-docker.png" width="600" </img>

```bash
docker pull nginx
docker run --name web -d -p 8080:80 nginx 
docker build -t larrycai/whoami .
docker push larrycai/whoami
```
.footnote[.red[*] Learn more in [CodingWithMe Docker]( https://www.slideshare.net/larrycai/learn-docker-in-90-minutes)
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
Master and Work Node (multi or single)

Container is executed in Work Node as default

<img src="https://storage.googleapis.com/cdn.thenewstack.io/media/2016/11/Chart_02_Kubernetes-Architecture.png" width="560" />

]

---
.left-column[
## Exercise 1
### - First web service
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
.left-column[
## Pod
]
.right-column[
A .red[**pod**] is a group of one or more containers (such as Docker containers), the shared storage for those containers

Minimal element in kubernetes, run in one Node

Command 
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
]
.right-column[
.red[**Service**] is an abstraction which defines a logical set of Pods and a policy by which to access them 
- sometimes called a micro-service
- Service could be selected by label (skipped here)

.red[**ServiceTypes**] defines how to expose a Service, The default is `ClusterIP` (internal)
- NodePort : expose port in Kubernetes Master
```
kubectl expose deployment xxx –-type=NodePort
```
- Load Balancer (need support in k8s infra)
- ...
]
---
.left-column[
## Exercise 2
### - revisit pod, deployment, service
]
.right-column[
Enter into container inside pod
```bash
kubectl exec -it nginx-xxx -- bash
```
Start pod only without deployment
```bash
kubectl run nginx --image=nginx --port=80 --restart=Never
kubectl run -i --tty ubuntu --image=ubuntu --restart=Never -- sh
```
Expose to another service
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
.left-column[
## Exercise 3
### - deployment with scale
]
.right-column[
Show hostname ( image: `larrycai/whoami` )
```bash
kubectl run whoami --image=larrycai/whoami --port=5000
kubectl expose deploy whoami --type=NodePort
```
- Check the hostname in webpage

Delete
```bash
kubectl delete pods whoami-xxxx
```
- Check the hostname in webpage (reload) 

Scale
```bash
kubectl scale --replicas=20 deployment/whoami
kubectl rollout status deployment/whoami
kubectl get pods –o wide
kubectl scale --replicas=2 deployment/whoami
```
- Check the hostname in webpage (reload) 

]
---
.left-column[
## Reference

<img src="https://images.manning.com/720/960/resize/book/d/e052c08-1751-4a4b-b95e-f1de5a715aa8/Luksa-Kubernetes-MEAP-HI.png" width="200"></img>
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
name: last-page
template: inverse

## That's all folks (for now)!

Slideshow created using [remark](http://github.com/gnab/remark).

