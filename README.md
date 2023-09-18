# blueGreenWithAgroCD
Blue green deployment is an application release model that gradually transfers user traffic from a previous version of an app or microservice to a nearly identical new release—both of which are running in production.</br>

## Argo CD Setup
Pre-requistes : </br>
- Any Kubernetes cluster (EKS, AKS, GKE)  
- Ingress controller integration: NGINX, ALB 
- Service Mesh integration: Istio, Linkerd, SMI
- Metric provider integration: Prometheus, Wavefront, Kayenta, Web, Kubernetes Jobs

We are using "EKS with Nginx ingress"</br>
Custom Domain = ```<Your Domain or URL of laod-balancer>```


Connecting with my EKS Client (Bastion HOST): </br>
 ```ssh -i "PEM-FILE-NAME" user@linuxconfig.org```


To see the dashboard of Argoroll-out. Get the Public IP of EKS Client (Bastion HOST) and use port number 3100. Example= "Public IP:3100" </br>

Installation of Argo Rollouts controller </br>

- Create the namespace for installation of the Argo Rollouts controller </br>
```kubectl create namespace argo-rollouts```

- You will see the namespace has been created </br>
```kubectl get ns argo-rollouts```

- we will use the latest version to install the Argo Rollouts controller. </br>
```kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml```

- You will see the controller and other components have been deployed. Wait for the pods to be in the Running state. </br>
```kubectl get all -n argo-rollouts```

- Install Argo Rollouts Kubectl plugin with curl for easy interaction with Rollout controller and resources. </br>
```curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64```
```chmod +x ./kubectl-argo-rollouts-linux-amd64```
```sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts```
```kubectl argo rollouts version```

- Argo Rollouts comes with its own GUI as well that you can access with the below command </br>
```kubectl argo rollouts dashboard```
  ```Public IP:3100``` (Public IP of EKS Client (Bastion HOST) and 3100 is port number of Argo Dashboard) </br>

## How to deploy 
- To keep things simple, let’s create all these objects for now in the default namespace by running the below commands:
###### ("argo-rollout/blueGreen/" is the name of the directory  where all my YAML filre placed) </br>
```kubectl apply -f argo-rollout/blueGreen/ ``` 

- See all the objects created in the default namespace by running the below commands: </br>
```kubectl get all```

- Now, you can access your sample app, by URL. </br>
```<Domain Name or URL of laod-balancer>```

- See the current status of this rollout by running the below command as well </br>
```kubectl argo rollouts get rollout rollout-bluegreen```

- Deploy the Green version of the app via command line </br>
```kubectl argo rollouts set image rollout-bluegreen rollouts-demo=argoproj/rollouts-demo:green```
 
- See new version(green) based set of pods of our sample app, coming up </br>
```kubectl get pods```

- If you visit the application domian. you would still see only the blue version is visible rightly because we have not yet fully promoted the green version of our app. </br>

   You can confirm the same now, by running the command below, which shows, the new version is in paused state. </br>
```kubectl argo rollouts get rollout rollout-bluegreen```

- Now, lets promote the green version of our app, by executing below command </br>
```kubectl argo rollouts promote rollout-bluegreen```

- Run the following command and you would see it's scaling the new i.e green version of our app completely. </br>
```kubectl argo rollouts get rollout rollout-bluegreen``` 

**** Congratulations!! you have successfully completed the blue-green deployment using Argo Rollouts. </br>

- Deleting the entire setup by using the below command. </br>
```kubectl delete -f argo-rollout/blueGreen/```

