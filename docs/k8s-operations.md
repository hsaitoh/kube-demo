#  Kubernetes App Operations

## Scale the number of Pods:

Check the # of wildfly pod by running **kubectl get po**:
```
kubectl get po

(SAMPLE OUTPUT)
NAME                         READY     STATUS    RESTARTS   AGE
modcluster-500718032-kfb17   1/1       Running   0          2h
omsagent-gj322               1/1       Running   0          16m
omsagent-hsc5r               1/1       Running   0          51m
wildfly-1364584080-2qswl     1/1       Running   0          1h
```

Or you can check by running **kubectl get deploy**:
```
kubectl get deploy wildfly

(SAMPLE OUTPUT)
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
wildfly   1         1         1            1           1h
```

So you have 1 pod for wildfly. Then, if you want to scale the # of pods to 3, run the following command:
```
kubectl scale --replicas=3 deploy wildfly
```

Check the # of wildfly pod again:
```
kubectl get deploy wildfly

(SAMPLE OUTPUT)
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
wildfly   3         3         3            3           1h
```

## Update the kubernete app:

First of all, prepare a new container image for the app and push it to a container registry. 

### Option1: Update the app by running "kubectl set image":

Suppose you upgrade the container image for the app from tag version 1.0 to 1.1, run the following command:

```
kubectl set image deploy wildfly wildfly=<acrLoginServer>/yoichikawasaki/wildfly-ticketmonster-ha:1.1 --record
```
### Option2: Update the app by running "kubectl apply":

Suppose you upgrade the container image for the app from tag version 1.0 to 1.1, Replace the container image part of kubernetes/wildfly-server.yaml file with the container name:tag:

```
containers:
- name: wildfly
    image: yoichikawasaki/wildfly-ticketmonster-ha:1.1
```

Then, run the following command to update the app in your kubernete cluster:
```
kubectl apply -f <repodir>/kubernetes/wildfly-server.yaml --record
```

You can check if it's actually updated in the cluster by running **kubectl describe** like this:
```
kubectl describe deploy wildfly

(SAMPLE OUTPUT)
...
   wildfly:
    Image:  yoichikawasaki/wildfly-ticketmonster-ha:1.1
...
```

## Get a shell to the running Container

Get a list of Pods by running **kubectrl get po**:
```
kubectrl get po

(SAMPLE OUTPUT)
NAME                         READY     STATUS    RESTARTS   AGE
modcluster-500718032-0d7sv   1/1       Running   0          10h
omsagent-qgsgq               1/1       Running   0          10h
wildfly-1364584080-01z1x     1/1       Running   0          10h
wildfly-1364584080-bb2jt     1/1       Running   0          10h
wildfly-1364584080-hzqq0     1/1       Running   0          10h
```
Suppose you want to get a shell to the running Container in a Pod named wildfly-1364584080-01z1x (a Pod that has one Container), run the following command:
```
kubectl exec -it wildfly-1364584080-01z1x -- /bin/bash
```
See also [Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) to lean more about the command.


## Cleanup all k8s objects selected by labels

Cleanup all objects that has a label "context=AKSDemo"
```
kubectl delete svc,deploy,ds -l context=AKSDemo
```
