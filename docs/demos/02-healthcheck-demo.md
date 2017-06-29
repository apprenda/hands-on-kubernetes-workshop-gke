# Healthcheck Demo Workflow

Here we will see high availability of a site after a failed deployment.  

These steps are to be executed from your local machine!

## 1. Navigate to the repository directory on your machine.  

```
$ cd /[LOCATION YOU CLONED THIS REPO]/GKE-hands-on-training
```

## 2. Execute the Kubernetes service and healthy deployment

```
$ kubectl apply -f examples/healthcheck/service.yaml
$ kubectl apply -f examples/healthcheck/healthy-deployment.yaml
```

## 3. Display the pods running which are serving our application

Execute the following command:

```
$ kubectl get pods -o wide
```

You should now see

```
NAME                           READY     STATUS    RESTARTS   AGE       IP               NODE
probes-demo-1216114202-fkbjn   0/1       Running   0          13s       172.16.235.211   worker1
probes-demo-1216114202-jl08v   0/1       Running   0          13s       172.16.235.212   worker1
probes-demo-1216114202-wv5jx   0/1       Running   0          13s       172.16.235.210   worker1
```

## 4. Browse to the Kubernetes Dashboard

if you closed the Kuberentes Dashboard, follow the instructions to open Dashboard [here](https://github.com/chrisgaun/GKE-hands-on-training/blob/master/docs/3-build-cluster.md)

## 5. Find the port and external IP

```
$ kubectl get services probes-demo
```

You should now be seeing:

```
root@bootstrap-node:~/hands-on-with-kubernetes-workshop# kubectl get services
NAME                CLUSTER-IP       EXTERNAL-IP            PORT(S)        AGE
k8s-workshop-site   172.17.149.128   104.196.252.72         80:32233/TCP   13s
```

## 8. Now deploy the broken deployment

```
$ kubectl apply -f examples/healthcheck/broken-deployment.yaml
```

## 9. Refresh the Kubernetes dashboard

Now refresh the Kubernetes dashboard displaying the pods a few times

You should start to see the warning message `Liveness probe failed: HTTP probe failed with statuscode: 404`  You will see that some of the pods have failed to start.

## 10. Refresh the browse displaying the website

You should still see "version 1.0" displayed on the webpage.

Kubernetes health checks have failed on the new pods so version 1.1 of the website isn't healthy so no traffic is being sent to it.  Kubernetes is preventing a rolling update from happening until the new pods are known to be healthy.

## 11. Delete the demo

Finally execute the following command to tidy away the demo:

```
$ kubectl delete -f examples/healthcheck/service.yaml
$ kubectl delete -f examples/healthcheck/broken-deployment.yaml
```
