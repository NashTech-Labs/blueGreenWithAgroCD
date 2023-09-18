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

1.Create the namespace for installation of the Argo Rollouts controller 
```kubectl create namespace argo-rollouts```

2.You will see the namespace has been created
```kubectl get ns argo-rollouts```

3.we will use the latest version to install the Argo Rollouts controller. 
```kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml```

4.You will see the controller and other components have been deployed. Wait for the pods to be in the Running state.
```kubectl get all -n argo-rollouts```

5. Install Argo Rollouts Kubectl plugin with curl for easy interaction with Rollout controller and resources.
```curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64```
```chmod +x ./kubectl-argo-rollouts-linux-amd64```
```sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts```
```kubectl argo rollouts version```

6. Argo Rollouts comes with its own GUI as well that you can access with the below command
```kubectl argo rollouts dashboard```
  ```Public IP:3100``` (Public IP of EKS Client (Bastion HOST) and 3100 is port number of Argo Dashboard) </br>

## How to deploy
1. To keep things simple, let’s create all these objects for now in the default namespace by running the below commands:
###### ("argo-rollout/blueGreen/" is the name of the directory  where all my YAML filre placed)
```kubectl apply -f argo-rollout/blueGreen/ ``` 

2. See all the objects created in the default namespace by running the below commands:
```kubectl get all```

3. Now, you can access your sample app, by URL.
```<Domain Name or URL of laod-balancer>```

4. See the current status of this rollout by running the below command as well
```kubectl argo rollouts get rollout rollout-bluegreen```

5. Deploy the Green version of the app via command line
```kubectl argo rollouts set image rollout-bluegreen rollouts-demo=argoproj/rollouts-demo:green```

6. See new version(green) based set of pods of our sample app, coming up 
```kubectl get pods```

7. If you visit the application domian. you would still see only the blue version is visible rightly because we have not yet fully promoted the green version of our app.

   You can confirm the same now, by running the command below, which shows, the new version is in paused state.
```kubectl argo rollouts get rollout rollout-bluegreen```

8. Now, lets promote the green version of our app, by executing below command
```kubectl argo rollouts promote rollout-bluegreen```

9. Run the following command and you would see it's scaling the new i.e green version of our app completely.
```kubectl argo rollouts get rollout rollout-bluegreen```

**** Congratulations!! you have successfully completed the blue-green deployment using Argo Rollouts. 

10. Deleting the entire setup by using the below command. 
```kubectl delete -f argo-rollout/blueGreen/```

