# Launching Resources Through Configuration Files
Last section was fun but it's not really sustainable. I mean you are not going to be running commands on the terminal for everything you want to deploy, and you won't be tweaking those commands for every little change you want to make. To describe our application configurations and properties we use configuration files, these are YAML files that describe what we deploy into kubernetes.

To follow this exercise you need to have your terminal in the `exercises/part_2` directory and your Minikube cluster started and ready to go.

## Deployment
In the last section we created a `Deployment` and a `Service`. This time we'll do the same but using configuration files. Let's start with the deployment. This is what a kubernetes deployment configuration file looks like:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80

```
To deploy this file run the following command:
```
kubectl apply -f assets/deployment.yaml
```
Check the created resources by running:
```
kubectl get deploy,pod
```
You should see something like this:
```
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   1/1     1            1           101s

NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-5c559d5697-nbwzb   1/1     Running   0          101s
```
One of the cool things about configuration files is that we can modify them and then apply updates directly to the already created resources. To try this open the file `assets/deployment.yaml` and on `line 8` change `replicas: 1` to `replicas: 2` and re-apply the file with:
```
kubectl apply -f assets/deployment.yaml
```
Now when you run `kubectl get pods` you'll see that you have two nginx pods running.
```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5c559d5697-lwjvg   1/1     Running   0          3s
nginx-deployment-5c559d5697-nbwzb   1/1     Running   0          4m32s
```
## Service
Now we will deploy the service file, for this run the following command:
```
kubectl apply -f assets/service.yaml
```
And let's check that our services was deployed correctly:
```
kubectl get svc
```
From the last section you'll remember how to test your newly deployed service from the browser ;)

## Clean It Up
Now before we continue let's delete everything we have deployed so far:
```
kubectl delete -f assets/deployment.yaml
kubectl delete -f assets/service.yaml
```
You might be thinking, running these commands from every single file could get a little bit tedious, and you are absolutely right. Fortunately every configuration file in a directory with a single command. Let's try it:
```
kubectl apply -f assets/
```
And the same to delete them:
```
kubectl delete -f assets/
```

# Exercise
Using the files under the `assets/` directory as templates modify them to try the following things:
* Try different replica counts in `assets/deployment.yaml`
* Use a different docker image that exposes a service in a different port
* Try what happens if you change the value of the `selector` key in `assets/service.yaml` and then try to consume the service in your browser
* Try creating a deployment that runs more than 1 container in a single pod

[Go back...](../README.md)
