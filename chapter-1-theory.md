# Chapter 1 Theory



---

## Architecture Diagram


```mermaid
graph LR
  A[Akeyless SaaS] -->|Secrets| B(External Secrets Operator (ESO))
  B --> C[Kubernetes Secret]
  C --> D[AKS Pods]
```


---

Content missing.
