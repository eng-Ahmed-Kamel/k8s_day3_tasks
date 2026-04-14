# Kubernetes Networking Lab – DNS, Services, and Ingress

## Description
This repository contains the implementation of **Kubernetes Lab 3**, focusing on Kubernetes networking concepts including **DNS-based service discovery, Services, and Ingress routing**.

The lab demonstrates how to deploy applications inside a Kubernetes cluster, expose them using different service types, verify internal DNS resolution, and configure **Ingress for path-based traffic routing**.

---

# Architecture

```
                +-----------------------------+
                |        Client Browser       |
                |  http://world.universe.mine |
                +--------------+--------------+
                               |
                               |
                         Ingress Controller
                               |
        +----------------------+----------------------+
        |                                             |
  /europe path                                   /africa path
        |                                             |
   Europe Service                               Africa Service
    (ClusterIP)                                   (ClusterIP)
        |                                             |
  Europe Deployment                              Africa Deployment
     (2 Pods)                                        (2 Pods)

---------------------------------------------------------------

                Kubernetes Internal DNS Example

              Test Pod (curl)
                    |
                    |
           web.iti.svc.cluster.local
                    |
               NodePort Service
                 Port 5000
                    |
               Web Deployment
                 (2 Pods)
```

---

# Tasks

## 1. DNS (10 Points)

### Deployment
- Create a **Deployment** called `web` in namespace `iti46`.
- Configure **2 replicas**.
- Application runs on **port 80**.

### Service
- Create a **NodePort Service** to expose the deployment.
- Service port should be **5000**.

### DNS Verification
- List the **SRV DNS record** of the service.
- Verify it resolves to the service domain inside the cluster.

### Connectivity Test
- Create a **test Pod** in the `default` namespace containing `curl`.
- Exec into the pod.
- Access the service using its **Kubernetes DNS name**.

---
## Task 1 Screenshots

### Step 1
![Step 1](task1/1--2026-04-13%2014-28-29.png)

### Step 2
![Step 2](task1/2--2026-04-13%2014-31-27.png)

### Step 3
![Step 3](task1/3---2026-04-13%2014-33-25.png)
---
## 2. Ingress and Services (15 Points)

### Namespace
Create namespace:

```
kubectl create namespace iti-46
```

### Deployments
Create two deployments in the `world` namespace:

| Deployment | Replicas | Image |
|------------|----------|-------|
| africa     | 2        | husseingalal/africa:latest |
| europe     | 2        | husseingalal/europe:latest |

---

### Services

Expose both deployments using **ClusterIP services**:

| Service | Port | Target Port |
|--------|------|-------------|
| africa | 8888 | 80 |
| europe | 8888 | 80 |

Service names must match the deployment names.

---

### Ingress

Create an **Ingress resource** called:

```
world
```

Domain:

```
world.universe.mine
```

Add the domain to `/etc/hosts` pointing to the Kubernetes Node IP.

Example:

```
192.168.146.141 world.universe.mine
```

### Routing Rules

| URL | Destination |
|-----|-------------|
| /europe | europe service |
| /africa | africa service |

Example URLs:

```
http://world.universe.mine/europe/
http://world.universe.mine/africa/
```

---

# Commands

## Create Namespaces

```bash
kubectl create namespace iti46
kubectl create namespace world
```

---

## Create Web Deployment

```bash
kubectl create deployment web \
--image=nginx \
--replicas=2 \
-n iti46
```

---

## Expose NodePort Service

```bash
kubectl expose deployment web \
--port=5000 \
--target-port=80 \
--type=NodePort \
-n iti46
```

---

## Check DNS

```bash
kubectl exec -it <pod-name> -- nslookup web.iti.svc.cluster.local
```

---

## Create Curl Test Pod

```bash
kubectl run curlpod \
--image=curlimages/curl \
--restart=Never \
-- sleep 3600
```

Exec into pod:

```bash
kubectl exec -it curlpod -- sh
```

Test the service:

```bash
curl web.iti.svc.cluster.local
```

---

## Create Deployments

```bash
kubectl create deployment africa \
--image=husseingalal/africa:latest \
--replicas=2 \
-n world
```

```bash
kubectl create deployment europe \
--image=husseingalal/europe:latest \
--replicas=2 \
-n world
```

---

## Expose Services

```bash
kubectl expose deployment africa \
--port=8888 \
--target-port=80 \
--type=ClusterIP \
-n world
```

```bash
kubectl expose deployment europe \
--port=8888 \
--target-port=80 \
--type=ClusterIP \
-n world
```

---

# Screenshots

Add screenshots for the following commands:
---
![op](op-task2.png)
---
### Deployments
```bash
kubectl get deployments -A
```

### Pods
```bash
kubectl get pods -A
```

### Services
```bash
kubectl get svc -A
```

### Ingress
```bash
kubectl get ingress -A
```

### DNS Resolution
```bash
nslookup web.iti.svc.cluster.local
```

### Curl Test
```bash
curl web.iti.svc.cluster.local
```

### Ingress Access
Open in browser:

```
http://world.universe.mine/europe/
http://world.universe.mine/africa/
```

---

# Author
Ahmed Kamel
