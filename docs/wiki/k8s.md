
- To list all the running nodes:  _**kubectl get nodes**_  
    
- To list pods in particular namespace:  _**kubectl get pods -n**_  
    
- To list services:  _**kubectl get svc**_  

- To list namespaces:  _**kubectl get namespaces**_  
    
- To create namespace:   _**kubectl create namespace**_  
    
- To list services in particular namespace:  _**kubectl get svc -n**_  
    
- To describe a pod:  _**kubectl describe pod**_  
    
- To describe a service:  _**kubectl describe svc**_  
    
- To get output of pod in yaml format:  _**kubectl get pod -o yaml**_  
    
- To get services of services in yaml format:  _**kubectl get svc -o yaml**_  
    
- To get logs of a pod:   _**kubectl logs**_  
    
- To execute command inside a pod:   _**kubectl exec -it -- bash**_  
    
- To get cpu and memory used for a pod:   _**kubectl top pod**_  
    
- To create a resource from a yaml file:   _**kubectl create -f**_  
    
- To modify a resource from a yaml file:   _**kubectl apply -f**_  
    
- To delete a resource defined in yaml file:   _**kubectl delete -f**_  
    
- To get all deployment details running in all the namespace:  _**kubectl get all –all-namespace**_  
    
- To list ingress:   _**kubectl get ingress**_  
    
- To list ingress running in a particular namespace:   _**kubectl get ingress -n**_  
    
- To get events happening on a cluster:   _**kubectl get events**_  
    
- To list secrets running:   _**kubectl get secrets**_  
    
- To list secrets running in a namespace:  _**kubectl get secrets -n**_  
    
- To list all deployments:  _**kubectl get deployments**_  
    
- To list all deployments in a namespace:  _**kubectl get deployments -n**_  
    
- To edit deployments:  _**kubectl edit deployments**_  
    
- To edit a service:  _**kubectl edit svc**_  
    
- To list all HPA:  _**kubectl get hpa**_  
    
- To list all hpa in a namespace:  _**kubectl get hpa -n**_  
    
- To edit a hpa:  _**kubectl edit hpa**_  
    
- To list persistent volumes:  _**kubectl get pv**_  
    
- To list persistent volume claims:  _**kubectl get pvc**_  
    
- To get information about statefulset:  _**kubectl get statefulset**_  
    
- To list all running nodes:  _**kubectl get nodes**_  
    
- To know labels to the node:  _**kubectl get nodes --show-labels**_  
    
- To list pods running with node names or node IP:  _**kubectl get pods -o wide**_  
    
- To show the status of rolling update to a deployment:  _**kubectl rollout status deployments**_  
    
- To get rollout history of deployment:  _**kubectl rollout history deployments**_  
    
- To rollout to an previous version of deployments: _**kubectl rollout undo deployments   --to-revision**_  
      
      
      