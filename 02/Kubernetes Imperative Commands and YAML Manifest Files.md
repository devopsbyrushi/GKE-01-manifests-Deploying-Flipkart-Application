# Chapter 07: Kubernetes Imperative Commands and YAML Manifest Files

## Introduction

In this chapter, we will learn how to create Kubernetes resources using both Imperative Commands and YAML Manifest Files. As a DevOps Engineer, you should be comfortable with both approaches because interviews and real-time projects often require them.

---

## What are Imperative Commands?

Imperative commands allow us to create Kubernetes resources directly from the command line without writing YAML files.

For example, if we want to create a Pod:

```bash
kubectl run nginx-pod --image=nginx
```

To verify:

```bash
kubectl get pods
```

To delete:

```bash
kubectl delete pod nginx-pod
```

---

## How to Generate YAML from an Imperative Command?

Instead of creating the resource directly, we can generate a YAML file.

```bash
kubectl run nginx-pod --image=nginx --dry-run=client -o yaml
```

Save it to a file:

```bash
kubectl run nginx-pod --image=nginx --dry-run=client -o yaml > pod.yaml
```

This is a common interview question.

---

## Pod Manifest File

A Pod is the smallest deployable unit in Kubernetes.

Example Pod YAML:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

Create the Pod:

```bash
kubectl apply -f pod.yaml
```

Verify:

```bash
kubectl get pods
```

---

## Creating a Deployment

In real projects, we rarely create standalone Pods because Pods do not provide self-healing or scaling.

Instead, we use Deployments.

Create a Deployment using an imperative command:

```bash
kubectl create deployment nginx-deployment --image=nginx
```

Verify:

```bash
kubectl get deployments
```

---

## Generate Deployment YAML

```bash
kubectl create deployment nginx-deployment --image=nginx --dry-run=client -o yaml
```

Save it:

```bash
kubectl create deployment nginx-deployment --image=nginx --dry-run=client -o yaml > deployment.yaml
```

---

## Deployment Manifest File

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

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
        image: nginx
        ports:
        - containerPort: 80
```

Deploy:

```bash
kubectl apply -f deployment.yaml
```

Verify:

```bash
kubectl get deployments
kubectl get pods
kubectl get rs
```

---

## Creating a NodePort Service

A NodePort Service exposes the application using the Node IP Address and a Port Number.

Create using an imperative command:

```bash
kubectl expose deployment nginx-deployment --type=NodePort --port=80
```

Verify:

```bash
kubectl get svc
```

Example Output:

```text
NAME               TYPE       PORT(S)
nginx-deployment   NodePort   80:30080/TCP
```

Application Access:

```text
http://NodeIP:30080
```

---

## NodePort Service Manifest File

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-nodeport

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Deploy:

```bash
kubectl apply -f nodeport-service.yaml
```

---

## Creating a LoadBalancer Service

In cloud environments such as AWS, Azure, and GCP, we usually use LoadBalancer Services.

Create using an imperative command:

```bash
kubectl expose deployment nginx-deployment --type=LoadBalancer --port=80
```

Verify:

```bash
kubectl get svc
```

Wait for an External IP to be assigned.

Example:

```text
NAME               TYPE           EXTERNAL-IP
nginx-deployment   LoadBalancer   34.xx.xx.xx
```

Application Access:

```text
http://34.xx.xx.xx
```

---

## LoadBalancer Service Manifest File

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-loadbalancer

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
```

Deploy:

```bash
kubectl apply -f loadbalancer-service.yaml
```

---

## Verifying Resources

Useful commands:

```bash
kubectl get pods
kubectl get deployments
kubectl get rs
kubectl get svc
kubectl get all
```

Detailed information:

```bash
kubectl describe pod <pod-name>
kubectl describe deployment nginx-deployment
kubectl describe svc nginx-loadbalancer
```

View Logs:

```bash
kubectl logs <pod-name>
```

---

## Difference Between Pod and Deployment

### Pod

* Runs a single container
* No self-healing
* No scaling capability
* Suitable for testing

### Deployment

* Manages Pods automatically
* Supports self-healing
* Supports scaling
* Creates ReplicaSets automatically
* Suitable for production environments

---

## Difference Between NodePort and LoadBalancer

| NodePort                            | LoadBalancer                      |
| ----------------------------------- | --------------------------------- |
| Accessible through Node IP and Port | Accessible through External IP    |
| Mainly used for testing             | Used in production                |
| Requires Node IP                    | Uses Cloud Provider Load Balancer |
| Manual access method                | Easy external access              |

---

## Resource Cleanup

Delete Pod:

```bash
kubectl delete pod nginx-pod
```

Delete Deployment:

```bash
kubectl delete deployment nginx-deployment
```

Delete Services:

```bash
kubectl delete svc nginx-nodeport
kubectl delete svc nginx-loadbalancer
```

Delete using YAML files:

```bash
kubectl delete -f pod.yaml
kubectl delete -f deployment.yaml
kubectl delete -f nodeport-service.yaml
kubectl delete -f loadbalancer-service.yaml
```

---

## Interview Questions

### What is an Imperative Command?

An imperative command directly creates or modifies Kubernetes resources from the command line.

### What is a Declarative Approach?

A declarative approach uses YAML manifest files to define the desired state of resources.

### What is the difference between NodePort and LoadBalancer?

NodePort exposes an application through the Node IP and a port number, whereas LoadBalancer exposes an application through an external IP provided by the cloud provider.

### What is the difference between a Pod and a Deployment?

A Pod runs a container, while a Deployment manages Pods and provides self-healing, scaling, and rolling update capabilities.

---

## Summary

In this chapter, we learned:

* Kubernetes Imperative Commands
* Creating Pods
* Creating Deployments
* Generating YAML Files
* Creating NodePort Services
* Creating LoadBalancer Services
* Resource Verification Commands
* Resource Cleanup Commands
* Pod vs Deployment
* NodePort vs LoadBalancer

This chapter forms the foundation for learning ReplicaSets, Self-Healing, Rolling Updates, Rollbacks, Autoscaling, and Kubernetes Production Deployments.
