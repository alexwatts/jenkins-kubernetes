# jenkins-kubernetes

Want to run a jenkins controller and slaves against a kubernetes control plane? (like digital ocean) just use these templates.

The templates provide a Jenkins pod for the master controller, a persistent volume claim so the jenkins working directory is retained after pod restarting / moving of the pod to other nodes. There is a service account and role bindings as well that can be used to configure Kubernetes slaves within Jenkins, in the normal way (similar to https://www.youtube.com/watch?v=ZXaorni-icg), to allow dockerised builds that are executed away from the controller on ephemeral slaves.

There is also a service template you can use to expose jenkins on a NodePort (30000) as well as opening up the Jenkins JNLP port so the slaves can communicate with the controller.

There is also an ingress template you can use to expose jenkins via a load balanced service URL.

## Getting Started

Create the jenkins namespace.

```
kubectl create namespace jenkins
```

Create the jenkins Stateful Set.

```
kubectl create -f jenkins.yaml --namespace jenkins
```

Check the pod is running.

```
kubectl get pods -n jenkins
```

You should see some output similar to.

```
NAME            READY   STATUS    RESTARTS   AGE
jenkins-set-0   1/1     Running   0          2d19h
```

Create the service.

```
kubectl create -f jenkins-service.yaml --namespace jenkins
```

Get the IP to access Jenkins UI.

```
kubectl get nodes -o wide
```

You should see some output like:

```
NAME                   STATUS   ROLES    AGE    VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-fv2ocqhbe-uk4j3   Ready    <none>   5d9h   v1.21.9   10.106.0.9    138.68.174.253   Debian GNU/Linux 10 (buster)   4.19.0-17-cloud-amd64   containerd://1.4.12
pool-fv2ocqhbe-uk4j8   Ready    <none>   5d9h   v1.21.9   10.106.0.8    138.68.170.14    Debian GNU/Linux 10 (buster)   4.19.0-17-cloud-amd64   containerd://1.4.12
pool-fv2ocqhbe-uk4ju   Ready    <none>   5d9h   v1.21.9   10.106.0.7    138.68.168.45    Debian GNU/Linux 10 (buster)   4.19.0-17-cloud-amd64   containerd://1.4.12
```

Take one of the EXTERNAL-IP values and construct a browser URL like and try to access jenkins:

```
http://{EXTERNAL-IP}:30000
```

You should be prompted to enter an admin password to configure Jenkins for the first time.

You can find the name of the pod by issuing the same command as before to list pods. Then you can view the logs for that pod to find the admin password

```
kubectl get pods -njenkins
```

You should see the jenkins pod again.

```
NAME            READY   STATUS    RESTARTS   AGE
jenkins-set-0   1/1     Running   0          2d19h
```

To get the logs.

```
kubectl logs jenkins-set-0 -n jenkins
```

You should see some output similar to.

```
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

{jenkins_admin_password}
```

You should be able to enter that password into the UI and if everything was successful you should see the Jenkins home screen where you can now start configuring jobs

```
http://{EXTERNAL-IP}:30000
```