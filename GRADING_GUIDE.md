# Guestbook Final Project - Grading Submission Guide

## Overview
This guide will walk you through capturing all 10 terminal outputs required for your final project submission.

## Prerequisites
- IBM Cloud Shell access
- IBM Cloud Container Registry (ICR) access
- Kubernetes cluster (IKS or OpenShift) access
- Namespace: `sn-labs-user`
- Registry: `us.icr.io/sn-labs-user`

---

## Task 1: Submit the Dockerfile (1 Point)

### Command:
```bash
cat Dockerfile
```

### Expected Output to Submit:
```
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

---

## Task 2: Submit terminal output - ibmcloud cr images (1 Point)

### Commands:
```bash
# Login to IBM Cloud
ibmcloud login --apikey <your-api-key>
ibmcloud cr region us
ibmcloud cr login

# Build image v1
docker build -t guestbook:v1 .

# Tag for IBM Cloud Registry
docker tag guestbook:v1 us.icr.io/sn-labs-user/guestbook:v1

# Push image v1
docker push us.icr.io/sn-labs-user/guestbook:v1

# List images (CAPTURE THIS OUTPUT)
ibmcloud cr images
```

### Expected Output to Submit:
```
Listing images in registry 'us.icr.io' under account 'XXXXXXXX'...

Repository          Tag   Digest                       Created      Size
sn-labs-user/guestbook   v1    sha256:abc123def456...   2025-04-15   23.5 MB
```

---

## Task 3: Submit index.html code (1 Point)

### Command:
```bash
cat index.html
```

### Expected Output to Submit:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guestbook</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 600px;
            margin: 50px auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            background-color: white;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        h1 {
            color: #333;
            text-align: center;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            color: #555;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #007bff;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            width: 100%;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Guestbook</h1>
        <form id="guestbookForm">
            <div class="form-group">
                <label for="name">Name:</label>
                <input type="text" id="name" name="name" placeholder="Enter your name" required>
            </div>
            <div class="form-group">
                <label for="message">Message:</label>
                <input type="text" id="message" name="message" placeholder="Enter your message" required>
            </div>
            <button type="submit">Sign Guestbook</button>
        </form>
    </div>
    <script>
        document.getElementById('guestbookForm').addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Thank you for signing the guestbook!');
            this.reset();
        });
    </script>
</body>
</html>
```

---

## Task 4: Submit HPA with 0 replicas (1 Point)

### Commands:
```bash
# Set your Kubernetes context
kubectl config use-context <your-cluster-context>

# Create namespace if needed
kubectl create namespace sn-labs-user

# Deploy application
kubectl apply -f deployment.yml -n sn-labs-user
kubectl apply -f service.yml -n sn-labs-user

# Create HPA (CAPTURE THIS OUTPUT IMMEDIATELY)
kubectl apply -f hpa.yml -n sn-labs-user
kubectl get hpa -n sn-labs-user
```

### Expected Output to Submit:
```
NAME        REFERENCE              TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
guestbook   Deployment/guestbook   <unknown>/50%   0         5         0          5s
```

---

## Task 5: Submit autoscaling output (2 Points)

### Commands:
```bash
# In one terminal - start load generator
kubectl run -i --tty load-generator --image=busybox /bin/sh --restart=Never -n sn-labs-user
# Inside the pod, run:
while true; do wget -q -O- http://guestbook.sn-labs-user.svc.cluster.local; done

# In another terminal - monitor HPA and pods
watch -n 2 'kubectl get hpa -n sn-labs-user && echo "---" && kubectl get pods -n sn-labs-user'
```

### Expected Output to Submit (when autoscaling kicks in):
```
NAME        REFERENCE              TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
guestbook   Deployment/guestbook   250%/50%        0         5         3          2m
---
NAME                          READY   STATUS    RESTARTS   AGE
guestbook-7d9f6c8b5d-abc12    1/1     Running   0          45s
guestbook-7d9f6c8b5d-def34    1/1     Running   0          45s
guestbook-7d9f6c8b5d-ghi56    1/1     Running   0          30s
load-generator                1/1     Running   0          2m
```

---

## Task 6: Submit Docker push v2 output (2 Points)

### Commands:
```bash
# Stop the load generator (Ctrl+C) and clean up
kubectl delete pod load-generator -n sn-labs-user

# Update index.html to v2
cp index-v2.html index.html

# Build and push v2
docker build -t guestbook:v2 .
docker tag guestbook:v2 us.icr.io/sn-labs-user/guestbook:v2
docker push us.icr.io/sn-labs-user/guestbook:v2
```

### Expected Output to Submit:
```
The push refers to repository [us.icr.io/sn-labs-user/guestbook]
5f70bf18a086: Preparing
5e85d7e47b12: Preparing
5e85d7e47b12: Pushed
5f70bf18a086: Pushed
v2: digest: sha256:b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c size: 739
```

---

## Task 7: Submit kubectl apply output (1 Point)

### Commands:
```bash
# Update deployment.yml - change image from v1 to v2
# Edit the file or use sed:
sed -i 's/guestbook:v1/guestbook:v2/g' deployment.yml

# Apply the updated deployment (CAPTURE THIS OUTPUT)
kubectl apply -f deployment.yml -n sn-labs-user
```

### Expected Output to Submit:
```
deployment.apps/guestbook configured
```

---

## Task 8: Submit index.html v2 code (2 Points)

### Command:
```bash
cat index.html
```

### Expected Output to Submit:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guestbook – v2</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 600px;
            margin: 50px auto;
            padding: 20px;
            background-color: #e8f5e9;
        }
        .container {
            background-color: white;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            border: 2px solid #4caf50;
        }
        h1 {
            color: #2e7d32;
            text-align: center;
        }
        .version-badge {
            background-color: #4caf50;
            color: white;
            padding: 5px 10px;
            border-radius: 4px;
            font-size: 12px;
            display: inline-block;
            margin-bottom: 15px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            color: #555;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #4caf50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            width: 100%;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Guestbook – v2</h1>
        <form id="guestbookForm">
            <div class="form-group">
                <label for="name">Name:</label>
                <input type="text" id="name" name="name" placeholder="Enter your name" required>
            </div>
            <div class="form-group">
                <label for="message">Message:</label>
                <input type="text" id="message" name="message" placeholder="Enter your message" required>
            </div>
            <button type="submit">Sign Guestbook</button>
        </form>
    </div>
    <script>
        document.getElementById('guestbookForm').addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Thank you for signing the guestbook (v2)!');
            this.reset();
        });
    </script>
</body>
</html>
```

---

## Task 9: Submit rollout history with CPU changes (2 Points)

### Commands:
```bash
# First, update deployment with change cause for CPU
kubectl patch deployment guestbook -n sn-labs-user -p '{"spec":{"template":{"metadata":{"annotations":{"change-cause":"CPU limit update to 200m"}}}}}'

# Apply the v2 deployment again
kubectl apply -f deployment.yml -n sn-labs-user --record=true

# Wait for rollout to complete
kubectl rollout status deployment/guestbook -n sn-labs-user

# Show rollout history (CAPTURE THIS OUTPUT)
kubectl rollout history deployment/guestbook -n sn-labs-user
```

### Expected Output to Submit:
```
deployment.apps/guestbook
REVISION  CHANGE-CAUSE
1         <none>
2         CPU limit update to 200m
```

---

## Task 10: Submit ReplicaSets after rollback (2 Points)

### Commands:
```bash
# Rollback to previous version
kubectl rollout undo deployment/guestbook -n sn-labs-user

# Verify rollback - show ReplicaSets (CAPTURE THIS OUTPUT)
kubectl get rs -n sn-labs-user
```

### Expected Output to Submit:
```
NAME                    DESIRED   CURRENT   READY   AGE
guestbook-7d9f6c8b5d    1         1         1       15m
guestbook-8a2g7d9c6e    0         0         0       5m
```

---

## Summary Checklist

Before submitting, ensure you have captured all 10 outputs:

- [ ] Task 1: Dockerfile content
- [ ] Task 2: `ibmcloud cr images` showing v1
- [ ] Task 3: index.html with `<title>Guestbook</title>`
- [ ] Task 4: `kubectl get hpa` showing 0 replicas
- [ ] Task 5: `kubectl get hpa` & `kubectl get pods` showing autoscaling
- [ ] Task 6: `docker push` v2 with digest
- [ ] Task 7: `kubectl apply` showing "configured"
- [ ] Task 8: index.html with `<title>Guestbook – v2</title>`
- [ ] Task 9: `kubectl rollout history` with CPU changes
- [ ] Task 10: `kubectl get rs` after rollback

---

## Clean Up Commands (After Grading)

```bash
# Delete all resources
kubectl delete deployment guestbook -n sn-labs-user
kubectl delete service guestbook -n sn-labs-user
kubectl delete hpa guestbook -n sn-labs-user
kubectl delete namespace sn-labs-user
```

---

## Tips for Success

1. **Run commands in order** - Each task builds on the previous one
2. **Capture outputs immediately** - Some states (like HPA with 0 replicas) change quickly
3. **Wait for rollouts** - Use `kubectl rollout status` to ensure updates complete
4. **Verify before moving on** - Check each output matches the expected format
5. **Keep terminal session open** - Use multiple terminals to monitor different resources

---

## Troubleshooting

### HPA shows <unknown>/50%
- This is normal when first created. Wait 1-2 minutes for metrics to populate.

### Autoscaling not working
- Ensure metrics-server is installed: `kubectl get apiservice v1beta1.metrics.k8s.io`
- Check pod resource requests are set in deployment.yml

### Rollback not showing correct ReplicaSets
- Wait for the rollout to complete before rolling back
- Use `kubectl rollout status` to verify

### Image push fails
- Verify you're logged in: `ibmcloud cr login`
- Check namespace is correct: `ibmcloud cr namespace-list`

---

## Quick Reference Commands

```bash
# Monitor everything
watch kubectl get all -n sn-labs-user

# Check pod logs
kubectl logs -l app=guestbook -n sn-labs-user

# Describe resources for debugging
kubectl describe deployment guestbook -n sn-labs-user
kubectl describe hpa guestbook -n sn-labs-user

# Access the application
kubectl get svc guestbook -n sn-labs-user
# Use NodePort to access via browser
```

---

Good luck with your final project submission!
