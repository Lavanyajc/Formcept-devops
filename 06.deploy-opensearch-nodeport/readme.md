# OpenSearch Kubernetes Deployment

This repository contains Kubernetes manifests to deploy OpenSearch (v2.14.0) inside a Kubernetes cluster with a NodePort service to expose it externally.

---

## Prerequisites

- A working Kubernetes cluster (kubeadm.)
- `kubectl` CLI configured to access your cluster
- Internet connectivity to pull OpenSearch images from Docker Hub
- Sufficient resources (at least 2GB memory for OpenSearch pod)

---

## Files

- `opensearch-deployment.yaml`: Kubernetes Deployment manifest for OpenSearch single-node cluster.
- `opensearch-service.yaml`: Kubernetes Service manifest exposing OpenSearch via NodePort.

---

## Deployment Details

### 1. Namespace

The manifests deploy resources into the `opensearch` namespace. Create it if it doesn't exist:

```bash
kubectl create namespace opensearch
````

---

### 2. Deployment Manifest (`opensearch-deployment.yaml`)

* Uses image: `opensearchproject/opensearch:2.14.0`

* Runs a single replica of OpenSearch with:

  * Ports:

    * 9200 (REST API)
    * 9600 (metrics)

  * Environment variables:
    * `discovery.type=single-node` (single node cluster mode)
    * `plugins.security.disabled=true` (disables security plugin for simplified setup)
    * `OPENSEARCH_INITIAL_ADMIN_PASSWORD="Lavanyajc@11"` (admin password)
 ```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: opensearch
  namespace: opensearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: opensearch
  template:
    metadata:
      labels:
        app: opensearch
    spec:
      containers:
        - name: opensearch
          image: opensearchproject/opensearch:2.14.0
          ports:
            - containerPort: 9200
            - containerPort: 9600
          env:
            - name: discovery.type
              value: single-node
            - name: plugins.security.disabled
              value: "true"
            - name: OPENSEARCH_INITIAL_ADMIN_PASSWORD
              value: "Lavanyajc@11"
          resources:
            limits:
              memory: "2Gi"
              cpu: "1"
            requests:
              memory: "1Gi"
              cpu: "500m"
  ```
---

### 3. Service Manifest (`opensearch-service.yaml`)
* Defines a **NodePort** service exposing ports:
  * 9200 on node port 31200
 
```
apiVersion: v1
kind: Service
metadata:
  name: opensearch-nodeport
  namespace: opensearch
spec:
  selector:
    app: opensearch
  ports:
    - name: rest
      port: 9200
      targetPort: 9200
      nodePort: 31200  # pick a port between 30000-32767
    - name: metrics
      port: 9600
      targetPort: 9600
      nodePort: 31201
  type: NodePort
```

* This allows access to OpenSearch from outside the cluster via:
```
http://<node-ip>:31200
```

where `<node-ip>` is the internal IP of your Kubernetes node.

---

## How to Deploy
1. Apply the namespace (if not already created):

```bash
kubectl create namespace opensearch
```

2. Apply the deployment:
```bash
kubectl apply -f opensearch-deployment.yaml
```

3. Apply the service:
```bash
kubectl apply -f opensearch-service.yaml
```

---

## Verify Deployment

1. Check pods in the namespace:

```bash
kubectl get pods -n opensearch
```

You should see the `opensearch` pod in `Running` status.

2. Check services:
```bash
kubectl get svc -n opensearch
```

Ensure the service type is `NodePort` and ports 31200 (for REST) and 31201 (for metrics) appear under `PORT(S)` and `NODE-PORT`.

3. Test OpenSearch access (replace `<node-ip>` with your node's IP):

```bash
curl -u admin:Lavanyajc@11 http://<node-ip>:31200
```

You should get a JSON response with OpenSearch cluster details.

---

## Troubleshooting
* For network plugin issues (e.g., Calico errors), ensure mount propagation is shared:
```bash
sudo mount --make-shared /
```

* If the OpenSearch pod crashes with password errors, verify the environment variable `OPENSEARCH_INITIAL_ADMIN_PASSWORD` is set properly.

---

## Cleanup
To delete all OpenSearch resources:

```bash
kubectl delete namespace opensearch
```

---

## Get Node IP
Use the following to find your node IP:

```bash
kubectl get nodes -o wide
```

---

