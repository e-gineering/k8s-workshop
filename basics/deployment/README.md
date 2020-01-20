# Deployment Exercise

In this exercise, we are going to deploy the quintessential hello world application in k8s.

*If you are not familiar with a Deployment manifest you can see the explanation for each section of the manifest file [here](manifest.md). (Or you can skip ahead to the instructions on how to deploy the application using the manifest file)*

In the command line terminal (e.g., cmd, powershell, git bash, WSL terminal, etc.),  execute the  following command.  
```console
kubectl apply -f hello/hello-deployment.yaml
```
This creates a [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) resource that will in turn create the [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/). *(All of these objects are created in the default  namespace because we did not specify one.  Use the -n  option to specify a namespace on all the kubectl commands. In our private cloud, we require a namespace be provided in the manifest file metadata itself.)*

To see the list of your deployments and its statuses:
```console
kubectl get deployments
```
It should say `1/1`  on the `READY`  column for your deployment when all the pod is up and running. 

Alternatively, you can see the list of pods that are created by the deployment:
```console
kubectl get pod
```
This should also have a `READY` column that will say `1/1` when all the containers in the pod are up and running (in our example there's only 1).  The `readinessProbe` determines when a container is considered ready.

If you want to watch the pods or deployments until it is up and running you can pass the -w  or --watch ( CTRL+C  to exit). For example:
```console
kubectl get pod -w
```
This will block on that command, allowing you to see the status change without having to repeatedly execute the command.

You can check the logs of your pod:
```console
kubectl logs <pod_name>
```
Use the `<pod_name>` from when you listed the pods.

To follow/tail the logs add the -f  option ( CTRL+C  to exit):
```console
kubectl logs <pod_name> -f
```

Next, we are going to try to delete the pod.

First, get the list of pods
```console
kubectl get pods
```
Take note of the name of one of the `hello-` pods. Use the pod name for the command below.
```console
kubectl delete pod <pod_name>
```
List out the pods again.
```console
kubectl get pods
```
See what happens.
