# Formcept-devops
This repository contains all tasks and learning resources related to the Formcept DevOps internship program. The focus is on container technologies, Kubernetes, monitoring with Prometheus and Grafana, and deploying OpenSearch in various environments.

---

## Repository Structure

### 01\_Basics

* `chroot.md` — Explanation of `chroot` command and its role in containerization.
* `container runtime & engine.md` — Overview of container runtimes and engines concepts.

### 02\_docker-engine-containerd-setup

* `02.docker engine & containerd setup.md` — Step-by-step guide to set up Docker Engine with containerd as the container runtime.

### 03\_opensearch-dockercompose

* `03.opensearch-dockercompose.md` — Instructions and manifest files to deploy an OpenSearch cluster using Docker Compose.

### 04\_prom-grafana

* `prometheus-health.png` — Screenshot showing Prometheus health metrics.
* `prome-output.png` — Output screenshot of Prometheus queries.
* `grafana-visualise.png` — Example Grafana dashboard visualizing OpenSearch and Prometheus data.
* `readme.md` — Documentation related to Prometheus and Grafana setup.

### 05\_kuber-cluster-containerd

* `kubernetes-cluster_wth_cont.md` — Guide on creating Kubernetes cluster with containerd runtime using kubeadm.
* Various screenshots showing:

  * Correct repo version
  * System pods status
  * Pod network checks
  * kubeadm join command and kubectl node status

### 06\_deploy-opensearch-nodeport

* `06.deploy-opensearch-nodeport.md` — Steps to deploy OpenSearch inside Kubernetes cluster and expose it externally using NodePort service.

---

## Project Overview

This project is focused on mastering the core DevOps skills around containerization, orchestration, and monitoring. The tasks cover:

* Setting up container engines and runtimes from scratch.
* Deploying and managing OpenSearch clusters via Docker Compose and Kubernetes.
* Setting up monitoring and visualization using Prometheus and Grafana.
* Creating a Kubernetes cluster using kubeadm with containerd as the runtime.
* Exposing Kubernetes services externally for local access and testing.

---

## How to Use

1. Follow the documentation files step-by-step in the order of folder numbering (Basics → Docker setup → OpenSearch → Monitoring → Kubernetes cluster → Deployment).
2. Refer to screenshots for visual confirmation of your progress and system state.
3. Use the provided commands and manifests to replicate the environment on your local or cloud setup.
4. Customize configurations as needed to fit your environment.

---

## Tools & Technologies Used

* Docker Engine
* containerd
* Kubernetes (kubeadm)
* OpenSearch
* Prometheus
* Grafana

---

## Author
Lavanya J C
DevOps Intern — Formcept

