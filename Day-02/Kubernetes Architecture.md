![Kubernetes Architecture](https://raw.githubusercontent.com/BharathKumarReddy2103/Kubernetes/main/Day-02/kubernetes-architecture.png)

# 🚀 Introduction: Why Do We Need Kubernetes?

Imagine managing **hundreds of containers** running your production workloads. You manually configure and restart them, and if a single piece of bad code sneaks in — it can crash containers and potentially take down your whole environment. 😓

This is where **Kubernetes (K8s)** shines 🌟

Kubernetes is an **open-source container orchestration platform** designed to:

✅ Ensure **high availability**

✅ Provide **self-healing** capabilities

✅ Scale effortlessly 🔁

✅ Automate container management 💡

Let’s take a deep dive into the internal architecture of Kubernetes — the **Control Plane** and the **Worker Nodes** — to see how they collaborate to run your applications reliably.

---

## 🧠 Kubernetes Architecture Overview

Kubernetes is structured into two main sections:

1. **Control Plane** (a.k.a. Master Node)
2. **Worker Nodes** (where your apps actually run)

Each of these has specific components that perform different tasks to keep the cluster running smoothly.

---

## ⚙️ Control Plane Components (Master Node)

The **Control Plane** is responsible for the brainwork — managing the cluster and making decisions around scheduling, scaling, and health checks.

### 🔐 1. API Server (`kube-apiserver`)

* Acts as the **gateway** to your cluster.
* Receives requests (like `kubectl` commands) and validates them.
* Communicates with other components and stores the desired state in `etcd`.

```bash
kubectl get pods
```

➡️ goes to the API Server ➡️ validated ➡️ stored in `etcd`.

---

### 💾 2. etcd (Cluster Store)

* A **highly available key-value store** that stores all cluster data.
* Keeps track of configuration, resource states, secrets, and more.
* It's the **single source of truth** for the Kubernetes cluster.

---

### 📦 3. Scheduler (`kube-scheduler`)

* Assigns Pods to suitable **Worker Nodes** based on:

  * CPU and memory requirements
  * Taints and tolerations
  * Node affinity/anti-affinity
  * Availability

It ensures Pods are placed on the best node available — optimizing performance.

---

### 🧩 4. Controller Manager (`kube-controller-manager`)

* Monitors the **actual state** vs the **desired state**.
* Runs multiple controllers:

  * `NodeController` – Tracks node health
  * `ReplicationController` – Manages pod replicas
  * `EndpointsController` – Updates endpoints for services
  * `NamespaceController` – Handles namespace lifecycle
  * `ServiceAccountController` – Manages service accounts
  * `JobController` – Manages batch jobs

---

### ☁️ 5. Cloud Controller Manager (`cloud-controller-manager`)

* Integrates Kubernetes with **cloud-specific APIs** like AWS, Azure, or GCP.
* Manages cloud-native resources such as:

  * Load balancers
  * Volumes
  * Network interfaces

---

## 🧱 Worker Node Components

Worker nodes are the **muscle** of your Kubernetes cluster. They run the actual applications in containers.

Each node runs:

* A **kubelet**
* A **kube-proxy**
* A **container runtime**

---

### 🔧 1. Kubelet

* An agent that ensures the containers defined in Pod specs are **running and healthy**.
* Talks to the control plane to get work assignments.
* Reports back the status of nodes and pods.

---

### 🌐 2. Kube Proxy

* Manages network rules on the node.
* Enables **communication between services and pods**, inside and outside the cluster.
* Implements **service discovery** and **load balancing** by forwarding traffic correctly.

---

### 📦 3. Container Runtime

* Software that **runs containers** (e.g., Docker, `containerd`, CRI-O).
* Works with the kubelet to:

  * Pull images
  * Start/stop containers
  * Manage lifecycle

---

## 🔄 What Happens When You Deploy a Pod?

Here’s a simplified flow of what happens behind the scenes:

🔢 Step-by-Step:

1. You run `kubectl apply -f pod.yaml`.
2. The API Server receives the request.
3. It validates and persists the request in `etcd`.
4. The Scheduler picks a suitable Worker Node.
5. The Kubelet on that node pulls the image via the container runtime and starts the pod.
6. Kube Proxy sets up networking so the pod is reachable.
7. Controllers monitor and maintain the desired state continuously.

---

## 🧠 Key Takeaways

* Kubernetes ensures **automated, scalable, and resilient container orchestration**.
* The **Control Plane** handles decisions and state management.
* **Worker Nodes** execute your application workloads.
* The **API Server, etcd, Scheduler, Controllers, and Kubelet** work in harmony to manage the cluster.

---

## 🎯 Conclusion

Understanding the architecture of Kubernetes helps you troubleshoot better, design scalable systems, and deploy applications more confidently.

Start experimenting with small deployments and move towards production-grade clusters. Kubernetes may seem complex at first — but once you get hands-on, it becomes an essential tool in your DevOps toolkit. 🔧☁️

> ✨ Keep learning, keep building — one pod at a time.
