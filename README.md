# Introduction to K8
## What is K8
- Large scale orchestration tool used to control a large number of containers
- Allows you to build in damage control options too (such as self healing)
- Open source

## Benefits
- Automated rollouts and rollbacks
- Self-healing: Automatically restarts containers when they fail
- Horizontal scaling: scales application up and down quickly either manually or automatically

## Why should we use
![](images/image1.png)

If you're used to tools like Docker containers but want to scale up from there then K8 is a good tool for that. It gives you methods to also group containers in sets, more robust scheduling tools with pods and you can also deploy nodes which can be used to submanage containers on your behalf

# Use cases
- Hybrid deployment: deploy and manage containers both locally on your own hardware as well as on off site hardware such as cloud services
- Big data: working with big data applications such as Hadoop for the processing of large amounts of data is easier with K8 thanks to it's horizontal scaling
- Machine learning: Dynamically allocate training jobs to machines with strong GPU performance

## When not to use
- Building serverless services with the function as a service platform is difficult with K8
- If you're not using containers (docker specifically). K8 doesn't perform so well in other settings
- Small scale operations that don't need learning a new piece of software like K8

## What are the competitors of K8
- Lambda
- Heroku
- Elastic Container Service

## What are managed services for K8
- A software offering maintained by a third-party provider but can be used by an application
- An example of this would be AWS EC2, Azure SQL database

## What is the difference between K8 managed service and non managed K8 services
- If you decide not to manage with K8 you will have to manage with your providers own tools
- This will mean having to learn a new set of tools for each provider or in some cases, building your own

## Diagram
⚠️Goes here

Ensure all ports are available that we will be using to deploy our app: `3000`, `80` and `27017`

### Task
- Create a K8 deployment file called `nginx-deployment.yml`
- Use default nginx docker image in our first iteration
- We will create 2 replicas of this deployment
- `kubectl create -f file-name.yml`

### K8 deployment with 2 replicas/pods
⬇️
```yaml
selector: nginx
```
⬇️

For your deployment to launch publicly we need create a `nginx_service.yml` to connect with deployment to see it working globally

### `LoadBalancer`
A load balancer is there to make sure that your load is distributed accross your different containers. Without this, one container may get hit harder than others and crash
## K8 commands
- Always run `kubectl` to get the latest list of all the commands you can use!
- `kubectl explain` will also give you full documentation on how to use a command
- `kubectl get [name of resource]`
  - `kubectl get avc`
  - `kubectl get node`
  - `kubectl get pods`
    - `kubectl describe pod`
- `kubectl delete deployments nginx-deployment`
- `kubectl port-forward [service name] [destination]:[origin]` Use this to make sure your apps are going to the correct ports if they aren't appearing where you expect

## [Kompose](https://kompose.io/)
You can also use [Kompose](https://kompose.io/) to convert already existing Docker-compose files into K8s files

## Chron-jobs
These can be used to run jobs periodically:
- Load your file up with: `kubectl apply -f chron-job.yml`
- Display your list of jobs with: `kubectl get cronjob`
- Run all your jobs with: `kubectl get jobs --watch`

The format is familiar to the other .yml files whilst employing regular chron-job expressions!

```
# Cron schedule syntax
#      ┌────────────────── timezone (optional)
#      |      ┌───────────── minute (0 - 59)
#      |      │ ┌───────────── hour (0 - 23)
#      |      │ │ ┌───────────── day of the month (1 - 31)
#      |      │ │ │ ┌───────────── month (1 - 12)
#      |      │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
#      |      │ │ │ │ │                                   7 is also Sunday on some systems)
#      |      │ │ │ │ │
#      |      │ │ │ │ │
# CRON_TZ=UTC * * * * *
```

## HPA
![](https://d33wubrfki0l68.cloudfront.net/4fe1ef7265a93f5f564bd3fbb0269ebd10b73b4e/1775d/images/docs/horizontal-pod-autoscaler.svg)

Horizontal Pod Autoscaler makes it so that you can set a minimum and maximum amount of pods that are running to help with load. It stops you from going over with your resources but will help if your app gets hit with a lot of traffic at once

```yml
spec:
  maxReplicas: 9
  minReplicas: 2
  # Target your node app deployment so the hpa knows which
  # deployment to scale up on demand, scales down when no longer needed
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: node
  targetCPUUtilizationPercentage: 50
```

So you can easily make sure you don't go over your resources too!

## Services
![](https://d33wubrfki0l68.cloudfront.net/e351b830334b8622a700a8da6568cb081c464a9b/13020/images/docs/services-userspace-overview.svg)

- ClusterIP. Exposes a service which is only accessible from within the cluster.
- NodePort. Exposes a service via a static port on each node’s IP.
- LoadBalancer. Exposes the service via the cloud provider’s load balancer.
- ExternalName. Maps a service to a predefined externalName field by returning a value for the CNAME reco

## What is the port range for NodePort?
The default node port range for Kubernetes is `30000` - `32767`

## How does K8 self heal 
K8s monitors the status of pods, when it recognises a pod has gone down it will attempt to self recover by restarting the pod and providing detailed logs on what went wrong!