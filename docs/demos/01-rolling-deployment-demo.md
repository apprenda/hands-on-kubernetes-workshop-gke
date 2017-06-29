# Rolling Deployment Demo Workflow

Here we will see a standard blue-green rolling deployment where we change from an older version of an application to an updated one. 

We will see two ways of creating a service so that you become familiar with kubectl and YAML. 

Relevant Kubernetes documentation is found [here](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). Instructors will cover "Writing a Deployment Spec." 

These steps are to be executed from your local machine!

## 1. Navigate to the repository directory on your machine.  

```
$ cd /[LOCATION YOU CLONED THIS REPO]/hands-on-with-kubernetes-gke
```

## 2. Execute the Kubernetes deployment first

```
$ kubectl apply -f examples/rolling-deployment/deployment-v1.0.yaml
```

## 3. Display the pods

```
$ kubectl get deployments kdemo-dep
```
Decribe the deployment

```
$ kubectl describe deployments kdemo-dep
```
See all the pods running on the cluster

```
$ kubectl get pods -o wide
```

## 4. Create a Services with an external endpoint that we can access

```
$ kubectl expose deployment kdemo-dep --type=LoadBalancer --name=k8s-workshop-site-dev
```
Find the port and external IP

```
$ kubectl get services k8s-workshop-site-dev
```

You should now be seeing:

```
root@bootstrap-node:~/hands-on-with-kubernetes-workshop# kubectl get services
NAME                CLUSTER-IP       EXTERNAL-IP            PORT(S)        AGE
k8s-workshop-site   172.17.149.128   104.196.252.72         80:32233/TCP   13s
```

Navigate to the external-ip address with the port (104.196.252.72:80 in this example).

## 5. Deploy version 1.1 of the website

Execute the following command from your machine:

```
$ kubectl apply -f examples/rolling-deployment/deployment-v1.1.yaml
```
Check the pods that are running (either in UI or with command)

```
$ kubectl get pods -o wide
```

Now if we check the pods currently running we should see v1.1 of the application is coming online:

```
NAME                                 READY     STATUS              RESTARTS   AGE
kdemo-dep-1412125313-c9clv   1/1       Running             0          7m
kdemo-dep-1412125313-dlhsh   1/1       Terminating         0          7m
kdemo-dep-1412125313-lxtx3   1/1       Running             0          7m
kdemo-dep-1641828995-6z6xm   0/1       ContainerCreating   0          3s
kdemo-dep-1641828995-jjgtc   0/1       ContainerCreating   0          3s
```

## 6. Browse to version 1.1 of the website

Refresh the site in the browser. You should see version 1.1.

## 7. Delete the demo

Delete the Service

```
kubectl delete services k8s-workshop-site-dev
```

Finally delete the deployment

```
$ kubectl delete -f examples/rolling-deployment/deployment-v1.1.yaml
```
