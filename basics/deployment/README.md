# Deployment Exercise

In this exercise, we are going to deploy the quintessential hello world application in k8s.

*If you are not familiar with a Deployment manifest you can see the explanation for each section of the manifest file [here](manifest.md). (Or you can skip ahead to the instructions on how to deploy the application using the manifest file)*

In the command line terminal (e.g., cmd, powershell, git bash, WSL terminal, etc.),  execute the  following command.  
```console
kubectl apply -f hello/hello-deployment.yaml
```
This creates a Deployment resource that will in turn create the Pods.  (All of these objects are created in the default  namespace because we did not specify one.  Use the -n  option to specify a namespace on all the kubectl commands. In our private cloud, we require a namespace be provided in the manifest file metadata itself.)

To see the list of your deployments and its statuses:
```console
kubectl get deployments
```
It should say 1/1  on the READY  column for your deployment when all the pod is up and running. 
