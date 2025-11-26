# Chapter 6 Theory



---

## Architecture Diagram


```mermaid
graph LR
  Dev[AKS-Dev] -->|ArgoCD| Git[Git Repo]
  Prod[AKS-Prod] -->|ArgoCD| Git
  Git --> Akeyless[Separate Paths]
```


---

Content missing.
