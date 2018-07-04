# Launching resources through CLI
For this section we will be launching a simple nginx into our Minikube cluster using only `kubectl` commands. Let's start with the basics, launching a pod.

### Launching a Pod
We can launch a single nginx pod with the following command:
```
kubectl run nginx --image=nginx --image-pull-policy=Always --restart=Never
```
Check the status of your pod by running `kubectl get pods`. After a few seconds you should see something like this:
```
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          25s
```
As of right now we have an nginx pod running. Just a small inconvenience, currently it is only accessible through the clusters private network; but no worries, we will fix it shortly. First let's test something, run `kubectl delete pod nginx` to stop the nginx pod we just launched and then run `kubectl get pods` again. You'll see this:
```
No resources found.
```
You can run `kubectl get pods` as many times as you want, our nginx pod is not coming back. Let's move on to the next section.


### Deployment
A Kubernetes Deployment checks on the health of Pods and restarts the Pod’s Container if necessary. Deployments are the recommended way to manage the creation and scaling of Pods. We can create a kubernetes deployment with the following command:
```
kubectl run nginx --image=nginx:latest --port=80 --image-pull-policy=Always
```
Note that this time, two resources have been created, a `deployment` and a `pod`. We can see them by running `kubectl get pods,deployment`
```
NAME                     READY     STATUS    RESTARTS   AGE
nginx-6bfb654d7c-8s6b7   1/1       Running   0          1m

NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx     1         1         1            1           1m
```
Let's try deleting our nginx pod. Let's copy the pod's name we get from running `kubectl get pods` and then run `kubectl delete pod <pod-name>`. Note that this time when we delete the pod its getting replaced by the deployment.
```
NAME                     READY     STATUS        RESTARTS   AGE
nginx-6bfb654d7c-8s6b7   0/1       Terminating   0          6m
nginx-6bfb654d7c-wxltz   1/1       Running       0          7s
```
So the deployment guarantees that we will always have one nginx pod running, but we still can't access it. So let's get that fixed next.

### Service
A Kubernetes Service is an abstraction which defines a logical set of Pods and a policy by which to access them, sometimes called a micro-service. We are going to expose our nginx pod by creating a service with the following command:
```
kubectl expose deployment nginx --type=LoadBalancer
```
We can see the created service by running `kubectl get svc`
```
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        1h
nginx        LoadBalancer   10.103.208.117   <pending>     80:30786/TCP   7s
```
Now that we have our service we can access nginx through minikube by running the following command:
```
minikube service nginx
```
Congratulations, with that you have completed this exercise.

# Cleanup
```
kubectl delete svc nginx
kubectl delete deployment nginx
minikube stop
```
