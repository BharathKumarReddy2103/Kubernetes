# 🚀 How to Upgrade a Kubernetes Cluster on AWS EKS with Zero Downtime

Kubernetes releases new versions approximately every three months. As DevOps or Cloud Engineers, staying current with the latest supported versions is not only best practice—it's essential for maintaining security, compatibility, and access to the latest features.

In this guide, you'll learn how to perform an **enterprise-grade Kubernetes cluster upgrade on AWS EKS** with **zero downtime**, following real-world DevOps best practices.

---

## 🌟 Introduction

Upgrading a Kubernetes cluster, especially in production, demands careful planning and execution. Whether you're using **EKS**, **AKS**, or **GKE**, the overall upgrade approach remains quite similar. This article focuses on **AWS EKS** and includes:

- Detailed upgrade process
- Must-follow prerequisites
- Common pitfalls and how to avoid them
- Insights for interviews and real-world project scenarios

---

## 📌 Why Upgrading Kubernetes Is Important

- Kubernetes only supports the **latest three versions**.
- As of Kubernetes v1.32, supported versions are: `v1.32`, `v1.31`, and `v1.30`.
- Older versions may be unsupported by the community and tooling ecosystem.
- Upgrade ensures **security patches**, **API stability**, and **new features**.

---

## 🛠️ Prerequisites Before Cluster Upgrade

Here’s a checklist of **critical prerequisites** every DevOps/Cloud engineer must follow before triggering the upgrade:

### 1. 🧪 Test in Lower Environments

- Upgrade **dev**, then **staging**, then **pre-prod** clusters.
- Give at least **1–2 weeks buffer** before production upgrade.
- Run **end-to-end tests** or **functional tests** in each environment.

### 2. 🧯 Cordon Nodes (Optional but Recommended)

```bash
kubectl cordon <node-name>
````

* Prevent new pods from scheduling during upgrade.
* Ideal for high-stakes environments.

### 3. 📄 Read the Kubernetes Release Notes

* Watch for:

  * **API deprecations**
  * **Ingress API group changes**
  * **Security changes**
* Example: `extensions/v1beta1` → `networking.k8s.io/v1` for Ingress.

### 4. 🔁 Kubernetes Upgrades Are Irreversible

* Downgrade is **not supported**.
* A broken upgrade means reinstallation.

### 5. ✅ Ensure Version Compatibility

| Component            | Requirement                                   |
| -------------------- | --------------------------------------------- |
| Control Plane        | Must match Node group version                 |
| Node Groups          | Upgrade only if same version as control plane |
| kubelet & kube-proxy | Must be compatible with control plane         |
| Cluster Autoscaler   | Must match upgraded Kubernetes version        |
| Subnet Availability  | At least 5 **free IPs** in the subnet         |

---

## 🔄 Kubernetes Upgrade Process (EKS)

AWS EKS is a **managed control plane**, but **upgrade is manual**.

### 🔹 Step 1: Upgrade the Control Plane

Use `eksctl`:

```bash
eksctl upgrade cluster \
  --name <cluster-name> \
  --region <aws-region> \
  --version 1.31 \
  --approve
```

🎯 This only upgrades the **control plane**, not the nodes.

### 🔹 Step 2: Upgrade Node Groups (Data Plane)

Option 1: **Rolling Update** (Recommended for zero downtime)

* Update existing node group using AWS Console or CLI.
* Select "Rolling Update" to upgrade one node at a time.

Option 2: **Create New Node Group**

* Create a new group with upgraded version.
* Drain & delete the old node group.

Example:

```bash
eksctl create nodegroup \
  --cluster <cluster-name> \
  --name <new-group> \
  --node-ami auto \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 4
```

Option 3: **Custom Launch Templates or AMIs**

* If you're using custom AMIs, rebuild and update the version manually.
* Use `aws ec2 create-launch-template-version` to upgrade templates.

### 🔹 Step 3: Upgrade Add-ons (e.g., VPC CNI, kube-proxy)

* Go to EKS → Add-ons → Click “Update”
* Choose latest compatible version for:

  * `aws-node` (VPC CNI)
  * `kube-proxy`
  * `coredns`

---

## 📚 Real-World Considerations

* 🔐 **OIDC Setup**: Ensure IAM OIDC provider is attached for IAM role mapping.
* 🧩 **Check Add-on Compatibility**: Use “Upgrade Insights” tab in EKS console.
* 📊 **Understand Cluster Components**: Know your Helm charts, CRDs, controllers.
* 🧪 **Verify Post-Upgrade**:

  * Run automated regression tests
  * Validate ingress, service discovery, and monitoring tools (Prometheus, Argo CD, etc.)

---

## 💡 Best Practices

* Always **read release notes** for every minor version bump.
* Keep a **change log** and **rollback plan** ready.
* Maintain **cluster upgrade runbooks**.
* Use **rolling updates** to avoid disruption.
* Monitor cluster logs post-upgrade using CloudWatch/Grafana.
* Tag upgrade commits & changes in your Git repositories.

---

## 🔎 Interview Insights

If asked in interviews:

> “How do you upgrade a Kubernetes cluster with zero downtime on AWS?”

✅ Sample Answer:

> "First, validate compatibility across control plane, node groups, and autoscalers. I start with lower environments and validate with functional tests. Using eksctl, I upgrade the control plane, followed by rolling upgrades for node groups, and finally, upgrade the add-ons. I ensure 5 free IPs in subnets and always consult the Kubernetes release notes beforehand."

---

## ✅ Conclusion

Upgrading your Kubernetes cluster is a critical task for maintaining a healthy, secure, and scalable infrastructure. With proper planning, you can achieve a **zero-downtime upgrade** across environments. Follow the step-by-step process, test rigorously in lower environments, and document every phase for audit and reproducibility.

---

Thank you for reading. Feel free to ⭐ star the repo, fork it, or contribute with improvements.
