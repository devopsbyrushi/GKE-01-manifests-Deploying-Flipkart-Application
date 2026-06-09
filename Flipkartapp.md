# 🚀 Deploying Flipkart Application on Google Kubernetes Engine (GKE) with 3 Replicas

## Lab Objective

In this hands-on lab, you will learn:

* Deploying an application on GKE
* Creating a Kubernetes Deployment
* Creating a LoadBalancer Service
* Understanding Pods, Deployments, and ReplicaSets
* Testing Kubernetes Self-Healing
* Scaling Applications
* Viewing Logs and Resource Details
* Cleaning Up Resources

---

# Architecture

```text
User
  |
  v
LoadBalancer Service
  |
  v
Deployment
  |
  +-------------------+
  |                   |
  v                   v
Pod-1            Pod-2            Pod-3
(Replica)        (Replica)        (Replica)
```

Kubernetes ensures that 3 Pods are always running.

---

# Prerequisites

Verify Cluster Access:

```bash
kubectl get nodes
```

Expected Output:

```bash
NAME                                          STATUS
gke-public-cluster-default-pool-xxxxx         Ready
```

---

# Step 1: Create Deployment Manifest

Create deployment file:

```bash
vi deployment.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: flipkart-demo-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: flipkart-demo

  template:
    metadata:
      labels:
        app: flipkart-demo

    spec:
      containers:
      - name: flipkart-demo-container
        image: devopsbyrushi/flipkartdemo

        ports:
        - containerPort: 80
```

Save:

```bash
ESC
:wq
```

---

# Step 2: Create LoadBalancer Service

Create service file:

```bash
vi service.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: flipkart-demo-service

spec:
  type: LoadBalancer

  selector:
    app: flipkart-demo

  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Save:

```bash
ESC
:wq
```

---

# Step 3: Deploy the Application

Deploy:

```bash
kubectl apply -f deployment.yaml
```

Expected:

```bash
deployment.apps/flipkart-demo-deployment created
```

Verify:

```bash
kubectl get deployments
```

Expected:

```bash
NAME                        READY
flipkart-demo-deployment    3/3
```

---

# Step 4: Verify Pods

Check Pods:

```bash
kubectl get pods
```

Expected:

```bash
NAME                                          READY
flipkart-demo-deployment-xxxxx-abcde          1/1
flipkart-demo-deployment-xxxxx-fghij          1/1
flipkart-demo-deployment-xxxxx-klmno          1/1
```

Three Pods should be running.

---

# Step 5: Verify ReplicaSet

Check ReplicaSets:

```bash
kubectl get rs
```

Expected:

```bash
NAME                                DESIRED CURRENT READY
flipkart-demo-deployment-xxxxx      3       3       3
```

Describe ReplicaSet:

```bash
kubectl describe rs
```

Observe:

```text
Desired Replicas : 3
Current Replicas : 3
Ready Replicas   : 3
```

---

# Step 6: Create LoadBalancer Service

Deploy Service:

```bash
kubectl apply -f service.yaml
```

Verify:

```bash
kubectl get svc
```

Initially:

```bash
EXTERNAL-IP   <pending>
```

Wait 2–5 minutes.

Check again:

```bash
kubectl get svc
```

Expected:

```bash
NAME                    TYPE           EXTERNAL-IP
flipkart-demo-service   LoadBalancer   34.xxx.xxx.xxx
```

---

# Step 7: Access Application

Open browser:

```text
http://<EXTERNAL-IP>
```

Example:

```text
http://34.118.224.1
```

You should see the Flipkart Demo Application.

---

# Step 8: Verify All Resources

```bash
kubectl get all
```

Expected Resources:

* Deployment
* ReplicaSet
* Service
* Pods

---

# Step 9: Test Kubernetes Self-Healing

Check Pods:

```bash
kubectl get pods
```

Delete Pod-1:

```bash
kubectl delete pod <pod-name>
```

Example:

```bash
kubectl delete pod flipkart-demo-deployment-xxxxx-abcde
```

Immediately watch:

```bash
kubectl get pods -w
```

Observe:

```text
Pod Deleted
New Pod Created
ContainerCreating
Running
```

Kubernetes automatically recreates the Pod.

This feature is called:

## Self-Healing

---

# Step 10: Delete Two Pods

Delete multiple Pods:

```bash
kubectl delete pod pod1 pod2
```

Example:

```bash
kubectl delete pod flipkart-demo-deployment-xxxxx-fghij flipkart-demo-deployment-xxxxx-klmno
```

Watch:

```bash
kubectl get pods -w
```

Kubernetes immediately creates two new Pods.

Expected:

```text
Desired Pods = 3
Current Pods = 3
```

ReplicaSet maintains desired state.

---

# Step 11: Scale Application to 5 Replicas

Increase replicas:

```bash
kubectl scale deployment flipkart-demo-deployment --replicas=5
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
5 Running Pods
```

Check ReplicaSet:

```bash
kubectl get rs
```

Expected:

```text
DESIRED = 5
CURRENT = 5
READY = 5
```

---

# Step 12: Scale Down to 2 Replicas

Reduce replicas:

```bash
kubectl scale deployment flipkart-demo-deployment --replicas=2
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
2 Running Pods
```

---

# Step 13: View Logs

Get Pod Name:

```bash
kubectl get pods
```

View Logs:

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs flipkart-demo-deployment-xxxxx
```

---

# Step 14: Describe Resources

Describe Deployment:

```bash
kubectl describe deployment flipkart-demo-deployment
```

Describe ReplicaSet:

```bash
kubectl describe rs
```

Describe Service:

```bash
kubectl describe svc flipkart-demo-service
```

Describe Pod:

```bash
kubectl describe pod <pod-name>
```

---

# Step 15: Verify Endpoints

```bash
kubectl get endpoints
```

Expected:

```bash
flipkart-demo-service   10.x.x.x:80
```

---

# Step 16: Delete Service

```bash
kubectl delete svc flipkart-demo-service
```

Verify:

```bash
kubectl get svc
```

---

# Step 17: Delete Deployment

```bash
kubectl delete deployment flipkart-demo-deployment
```

Verify:

```bash
kubectl get deployments
```

---

# Step 18: Cleanup Everything

```bash
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
```

Verify:

```bash
kubectl get all
```

Expected:

```text
No resources found
```

---

# Key Learning Outcomes

✅ Deployment Creation

✅ Service Creation

✅ LoadBalancer Exposure

✅ ReplicaSet Management

✅ Self-Healing Demonstration

✅ Pod Recovery

✅ Scaling Up

✅ Scaling Down

✅ Viewing Logs

✅ Resource Troubleshooting

✅ Resource Cleanup


