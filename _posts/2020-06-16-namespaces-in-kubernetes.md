---
layout: post
title: Namespaces in Kubernetes
date: 2020-06-16 22:56 +0530
---

> Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.

In simple terms, Namespaces provide a way of re-using the same resource/object name in the same AKS cluster. It is important to note that the resouces would still run on the same Nodes, so they can still talk to each other.

### Imaging this

You have created a Web application using the microservice architecture via Spring Cloud. Generally, you would want to keep the running application untouched, and create another ***staging*** application with similar configurations.  

Every object that we create in Kubernetes gets created in a namespace called ***default***. You cannot have 2 pods with the same name in the same namespace. So, one way to have the staging application is to create each pod with a different name. Well, that would definitely work. At the same time, we might also want to assign different level of access to the production and the staging application. So, management becomes a difficult task in this case. The solution is to create another namespace. It's like a ***logical*** separation of different objects. So, you can have a pod called foo in the default namespace, and another one with the same name foo in a different namespace.

### Here is how it would look

- Create a namespace called staging:  

```kubectl
kubectl create namespace staging
```

- Get the list of namespaces:

```kubectl
kubectl get namespace
```

- Create a pod in the default namespace. You can use the following YAML file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual
spec:
  containers:
  - image: gkgaurav31/mynodeapp
    name: kubia
    ports:
      - containerPort: 8080
        protocol: TCP

```

Command:

```kubectl
kubectl create -f kubia-manual.yml
```

- Get the pods in default and staging namespace:

```kubectl
kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
kubia-manual   1/1     Running   0          7m20s
```

```kubectl
kubectl get pods --namespace staging
No resources found.
```

You could create another pod with the same name in staging namespace. If you try to do that in the default namespace, it would fail.

```kubectl
kubectl create -f kubia-manual.yml
Error from server (AlreadyExists): error when creating "kubia-manual.yml": pods "kubia-manual" already exists
```

```kubectl
kubectl create -f kubia-manual.yml --namespace staging
pod/kubia-manual created

kubectl get pods --namespace staging
NAME           READY   STATUS              RESTARTS   AGE
kubia-manual   0/1     ContainerCreating   0          4s
```

> In future versions of Kubernetes, objects in the same namespace will have the same access control policies by default.

Documentation: [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)