# Chapter 4 Theory



---

## Architecture Diagram


```mermaid
graph LR
  subgraph Production
    AKS[AKS Cluster]
    ESO[External Secrets Operator]
    Secrets[Kubernetes Secrets]
    Apps[Applications Pods]
  end
  AKS --> ESO
  ESO --> Secrets
  Secrets --> Apps
```


---

Content missing.
