## Real-Time Kubernetes Commands for Production-Level Troubleshooting

---

## 🌟 Introduction

Kubernetes is the backbone of modern container orchestration. However, mastering Kubernetes goes beyond deploying pods or services—it involves real-time troubleshooting, scaling, patching, and maintaining your clusters in production. This guide introduces you to **25+ production-grade `kubectl` commands** every DevOps Engineer must know, along with real examples, explanations, and use cases.

Whether you're preparing for a **DevOps interview**, **debugging a broken deployment**, or **working with Persistent Volumes**, this article will guide you step-by-step.

---

## 🛠️ Essential Kubernetes Commands with Real-Time Use Cases

### 1. 🔁 Restart a Deployment (Apply ConfigMap/Secret Changes)
```bash
kubectl rollout restart deployment <deployment-name> -n <namespace>
````

**Use case**: Apply changes from ConfigMap/Secrets without deleting pods manually.

---

### 2. 🔄 Rollback to Previous Stable Deployment

```bash
kubectl rollout undo deployment <deployment-name> -n <namespace>
```

**Use case**: Fix broken deployment by rolling back to the last known good state.

---

### 3. 📜 View Rollout History with Revisions

```bash
kubectl rollout history deployment <deployment-name> -n <namespace>
```

Add change-cause:

```bash
kubectl annotate deployment <deployment-name> kubernetes.io/change-cause="Reason for update"
```

---

### 4. 🔍 Check Mounted Volumes Inside Pod

```bash
kubectl exec -it <pod-name> -n <namespace> -- df -h
```

---

### 5. 🔓 Decode Kubernetes Secret

```bash
kubectl get secret my-secret -n <namespace> -o jsonpath="{.data.password}" | base64 --decode
```

---

### 6. 💥 Simulate a Pod Crash

```bash
kubectl exec -it <pod-name> -n <namespace> -- kill 1
```

---

### 7. 📄 View Logs from Previous Pod Crash

```bash
kubectl logs -p <pod-name> -n <namespace>
```

---

### 8. 🔬 Manually Trigger Readiness Probe

```bash
kubectl exec -it <pod-name> -n <namespace> -- wget localhost:80/login
```

---

### 9. 🌐 Test Internal DNS Resolution

```bash
kubectl run mysql-client --image=mysql -it --rm --restart=Never -- \
mysql -h mysql-service -u root -p<password>
```

---

### 10. 📋 Describe Pod Volumes & Mount Paths

```bash
kubectl describe pod <pod-name> -n <namespace>
```

---

### 11. 🌐 Get Service Endpoints & Linked Pod IP

```bash
kubectl get endpoints <service-name> -n <namespace>
kubectl get pods -o wide -n <namespace>
```

---

### 12. 📅 View All Events Chronologically in Namespace

```bash
kubectl get events -n <namespace> --sort-by='.lastTimestamp'
```

---

### 13. 🧵 Patch PVC to Increase Volume

```bash
kubectl patch pvc <pvc-name> -n <namespace> -p '{"spec":{"resources":{"requests":{"storage":"15Gi"}}}}'
```

Make sure storage class has:

```yaml
allowVolumeExpansion: true
```

---

### 14. 💣 Trigger CrashLoopBackOff

```bash
kubectl exec -it <pod-name> -n <namespace> -- kill 1
```

---

### 15. 📈 Enable Horizontal Pod Autoscaler (HPA)

```bash
kubectl autoscale deployment <deployment-name> -n <namespace> --cpu-percent=50 --min=1 --max=5
```

---

### 16. 📊 Live Monitor CPU & Memory Usage

```bash
watch kubectl top pod -n <namespace>
```

> Requires Metrics Server installed.

---

### 17. 💾 Backup Volume from Pod to Local Machine

```bash
kubectl cp <namespace>/<pod-name>:/var/lib/mysql ./mysql-backup
```

---

### 18. 🧯 Drain a Node for Maintenance

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

---

### 19. 🟢 Mark Node Ready for Scheduling Again

```bash
kubectl uncordon <node-name>
```

---

### 20. 🔁 Restart All Deployments in Namespace

```bash
kubectl rollout restart deployment --all -n <namespace>
```

---

### 21. 🧾 List All Deployments with Image Versions

```bash
kubectl get deployments -o=jsonpath="{range .items[*]}{.metadata.name} - {.spec.template.spec.containers[*].image}{'\n'}{end}" -n <namespace>
```

---

### 22. 🧭 Find Which Node Pod is Running On

```bash
kubectl get pod <pod-name> -o wide -n <namespace>
```

---

### 23. 🧹 Force Delete a Stuck Pod

```bash
kubectl delete pod <pod-name> -n <namespace> --grace-period=0 --force
```

---

### 24. 🧪 Compare Manifest vs Live Cluster

```bash
kubectl diff -f pvc.yaml
```

---

## 📌 Best Practices

* Always use `rollout restart` instead of deleting pods manually.
* Patch PVC only if `allowVolumeExpansion: true` is set in StorageClass.
* Use `kubectl exec -- kill 1` carefully—it will crash your pod.
* Use HPA with CPU metrics but consider custom metrics with Prometheus for advanced autoscaling.
* Keep metrics server installed for real-time resource monitoring.

---

## 🚀 Conclusion

These Kubernetes commands are your go-to toolbox for **real-world cluster troubleshooting, scaling, and maintenance**. Mastering these commands can help you perform confidently in production environments and DevOps interviews alike.

---

## 🙌 Like this article?

If you found this helpful, give a ⭐ on GitHub and share it with your DevOps community.
