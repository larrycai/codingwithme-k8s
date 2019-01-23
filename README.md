# codingwithme-k8s

some codes in CodingWithMe : Kubernetes

* slides online: https://larrycai.gitlab.io/codingwithme-k8s
* slides@slideshare: https://www.slideshare.net/larrycai/learn-kubernetes-in-90-minutes (it cannot be updated since 2017.10 due to silly slideshare)

# preparation in playground

http://labs.play-with-k8s.com/ is recommended to use, create 3 instance

* 1 instance as master node: follow steps for 1,2,3 (step 3 may have issues)
* 2 instance as worker nodes: just run `kubectl join` command from master node after step 1

## Step 3: dashboard
dashboard sometimes doesn't work, if not, use following command

    curl -L -s https://git.io/vdc52   | sed 's/targetPort: 9090/targetPort: 9090\n  type: LoadBalancer/' |  kubectl apply -f -
    
If you happen to install the dashboard in wrong status, clean it up

    kubectl delete deploy/kubernetes-dashboard  --namespace=kube-system
    kubectl delete svc/kubernetes-dashboard  --namespace=kube-system
    
# slides

try to use markdown slides [remark](https://github.com/gnab/remark)

## browse locally

    docker run -d -v $PWD:/code --name web -w /code -p 8080:8000 python:2.7 python -m SimpleHTTPServer

## generate pdf

     docker run --rm --net=host -v `pwd`:/slides \
        astefanutti/decktape http://www.larrycaiyu.com/codingwithme-k8s/slides.html slides.pdf

# Errata



