# Breaking down a deployment manifest

## Kind
The apiVersion  and kind, together, specifies the API and what kind of resource we are defining in the manifest file. We are creating a Deployment manifest file.

```yaml
apiVersion: apps/v1
kind: Deployment

## Metadata
The name uniquely identifies this Deployment. labels  are used for selectors on other resources and log queries.

```yaml
metadata:
  name: hello
  labels:
    app: hello
```
*(Note: In our private cloud, we are required to specify a namespace in the metadata. However, for our exercise we are just using the default  namespace.)*

## Spec
The spec section of a manifest file contains configurations for that resource type, in this case they are configuration for a deployment.

For example, the first thing we are going to configure is how many replicas (pods) this deployment will have.

```yaml
spec:
  replicas: 1
```

Next we need to specify the label selector that defines which pods are managed by this deployment, i.e., spec.selector.matchLabels.

```yaml
  selector:
    matchLabels:
      app: hello
```

## Pod Template
The next subsection is the spec.template  which contains the template of the Pod that will be created.

The spec.template.metadata.labels must match the selector.matchLabels  because this determines which pods the are managed by the deployment.

```yaml
  template:
    metadata:
      labels:
        app: hello
```

The pod template also has its own spec  section which defines the configuration of the pod.  This section is where you define the containers that will go into the pod.

A container requires a name field.

```yaml
    spec:
      containers:
      - name: hello-app
```

In the container section you'll need to specify which image to use for the container.

**TODO: Update image**
```yaml
        image: "egineering-llc/hello-micronaut:0.0.1-26_3e59c44"
```

The spec.template.spec.containers[].ports[] contains the list ports in the container that will be exposed. These exposed ports can then be bound to service ports (will be covered later).

```yaml
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
```

## Probes

The spec.template.spec.containers[].readinessProbe defines the check that helps kubernetes determine when a container is considered ready to receive requests (the service will start allowing traffic through).   In this case, it is using an http GET to perform a check.  It is considered a success when it returns a status code greater than or equal to 200 and less than 400.

```yaml
        readinessProbe:
          httpGet:
            path: /hello
            port: 8080
          initialDelaySeconds: 3
          periodSeconds: 10
```
The spec.template.spec.containers[].livenessProbe performs its checks in the same way that the readinessProbe is done. However, this probe check is used for self-healing instead of determining the readiness of the pod.  If the liveness check fails (i.e., does not return a status code greater than or equal to 200 and less than 400) it will kill the container and be subjected to its restart policy, which default to Always, which means it will perform a restart if it fails the liveness probe.  The initialDelaySeconds on the livenessProbe is set to a larger number than the readinessProbe to avoid a situation where the container is restarted before it is given a chance to fully startup.

```yaml
        livenessProbe:
          httpGet:
            path: /hello
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 30
```

# Resource Management
The spec.template.spec.containers[].resources is the section in the manifest that defines the management of the computer resources.  The limits is self explanatory; it is used to set policies for how much resources a container can use. The requests defines the minimum  resource requirements of a container which is used for determining the required resources for a node to host this pod.

The memory requests and limits are straightforward and can be taken as the minimum and maximum values of the memory resource, but the CPU values between requests and limits has some nuance that you can check out if you are interested. In simple terms, these values can be treated as minimum and maximum boundaries.

Mi on the memory values stands for MebiByte which differs from MegaByte in that a MegaByte is in the power of 10 whereas a MebiByte is in the power of 2.  1 MebiByte is equal to 1024 KibiByte whereas 1 MegaByte is 1000 KiloByte.  MebiByte is a more accurate unit to represent memory than the MegaByte but either can be used (i.e., you can use the suffix M  for MegaByte). 

The m unit for CPU stands for 1/1000 of a CPU core.  

```yaml
       resources:
         limits:
           memory: 100Mi
           cpu: 1
         requests:
           memory: 10Mi
           cpu: 10m
```
