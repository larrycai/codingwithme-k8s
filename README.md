# codingwithme-k8s

some codes in CodingWithMe : Kubernetes

slides: https://www.slideshare.net/larrycai/learn-kubernetes-in-90-minutes (it cannot be updated since 2017.10 due to slideshare)

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
    
# Errata



