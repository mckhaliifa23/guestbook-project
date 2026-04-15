# Guestbook Project - Final Project Submission

## Overview
This is a complete Guestbook web application for the Cloud Application Development final project. The project demonstrates Docker containerization, Kubernetes deployment, Horizontal Pod Autoscaling (HPA), and rolling updates/rollbacks.

## Project Structure

```
guestbook/
├── Dockerfile           # nginx:alpine container definition
├── index.html           # Version 1 of the Guestbook app
├── index-v2.html        # Version 2 of the Guestbook app (for update demonstration)
├── deployment.yml       # Kubernetes Deployment manifest
├── service.yml          # Kubernetes Service (NodePort)
└── hpa.yml              # Horizontal Pod Autoscaler configuration

GRADING_GUIDE.md         # Complete step-by-step submission guide
README.md                # This file
```

## Application Features

### Version 1
- Simple guestbook with form input
- Title: "Guestbook"
- Styled with clean blue theme

### Version 2
- Updated guestbook with version indicator
- Title: "Guestbook – v2"
- Styled with green theme to distinguish from v1

## Kubernetes Configuration

| Resource | Setting |
|----------|---------|
| Replicas | 1 (initial), 0-5 with HPA |
| CPU Request | 100m |
| CPU Limit | 200m |
| HPA Target | 50% CPU utilization |
| HPA Min/Max | 0 / 5 replicas |
| Service Type | NodePort (Port 80) |

## Quick Start in IBM Cloud Shell

```bash
# Clone the repository
git clone https://github.com/mckhaliifa23/guestbook-project.git
cd guestbook-project/guestbook

# Login to IBM Cloud
ibmcloud login --apikey <your-api-key>
ibmcloud cr login

# Build and push v1 image
docker build -t guestbook:v1 .
docker tag guestbook:v1 us.icr.io/sn-labs-user/guestbook:v1
docker push us.icr.io/sn-labs-user/guestbook:v1

# Deploy to Kubernetes
kubectl apply -f deployment.yml -n sn-labs-user
kubectl apply -f service.yml -n sn-labs-user
kubectl apply -f hpa.yml -n sn-labs-user

# Check deployment
kubectl get all -n sn-labs-user
```

## Final Project Submission

**Please refer to [GRADING_GUIDE.md](GRADING_GUIDE.md) for complete step-by-step instructions on capturing all 10 required terminal outputs for your submission.**

The grading guide includes:
- Exact commands to run for each task
- Expected outputs for verification
- Troubleshooting tips
- Clean up commands

## Tasks Overview

| Task | Description | Points |
|------|-------------|--------|
| 1 | Submit Dockerfile | 1 |
| 2 | Terminal output - ibmcloud cr images (v1) | 1 |
| 3 | Submit index.html (v1) code | 1 |
| 4 | Terminal output - HPA with 0 replicas | 1 |
| 5 | Terminal output - Autoscaling working | 2 |
| 6 | Terminal output - Docker push v2 | 2 |
| 7 | Terminal output - kubectl apply | 1 |
| 8 | Submit index.html (v2) code | 2 |
| 9 | Terminal output - Rollout history | 2 |
| 10 | Terminal output - ReplicaSets after rollback | 2 |

**Total: 15 points (70% = 10.5 points to pass)**

## Environment Variables

- **Namespace:** `sn-labs-user`
- **Registry:** `us.icr.io/sn-labs-user`
- **Image Name:** `guestbook`

## Cleanup

After grading, clean up resources:

```bash
kubectl delete deployment guestbook -n sn-labs-user
kubectl delete service guestbook -n sn-labs-user
kubectl delete hpa guestbook -n sn-labs-user
```

## Author

Created for Cloud Application Development Final Project

---

**Note:** For detailed submission instructions, see [GRADING_GUIDE.md](GRADING_GUIDE.md)
