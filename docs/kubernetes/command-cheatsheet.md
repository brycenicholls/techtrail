---
hide:
  - toc
---

# Command Cheatsheet

**create pod cli:**
```
kubectl run {pod name} --image={image name}
```

**generate yaml pod definition template:**
```
kubectl run {pod name} --image={image name} --dry-run -o yaml
```

**replica set:**
```
kubectl create -f {file name}
kubectl get replicaset
kubectl delete replicaset {name}
kubectl replace -f { file name}
kubectl scale replicaset --replicas={number} {replicaset name}
```
