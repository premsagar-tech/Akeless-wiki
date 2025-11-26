# Chapter 4 – Production Best Practices & Architecture Hardening

## 1. Introduction
This chapter focuses on **enterprise-grade production hardening** for AKS + Akeyless + ESO, covering:

- Security best practices  
- Operational excellence  
- Secret rotation models  
- High-availability patterns  
- Multi-cluster GitOps frameworks  
- Compliance & audit alignment  

---

## 2. Enterprise Production Architecture

```
                 ┌────────────────────────────────────────┐
                 │              Akeyless SaaS             │
                 │  Zero-Knowledge Vaultless DFC Engine   │
                 │  + Audit Logs + IAM + Dynamic Secrets  │
                 └────────────────────────────────────────┘
                               ▲               ▲
                               │               │
                     Private Gateway      SaaS Access Point
                               │               │
 ┌─────────────────────────────┼───────────────┼──────────────────────────────┐
 │                         Cloud Network (Azure VNet)                         │
 └───────────────────────▲───────────────▲────────────────────────────────────┘
                         │               │
            ┌────────────┴──────────┐  ┌─┴─────────────────┐
            │        AKS Cluster     │  │   GitOps System   │
            │  ESO + Workloads + SA │  │ (ArgoCD / FluxCD) │
            └────────────┬──────────┘  └────────────────────┘
                         │
                Kubernetes Secrets
                         │
                         ▼
                 Application Pods
```

---

## 3. Hardening AKS for Akeyless + ESO

### 3.1 Enable OIDC Issuer on AKS
Required for Akeyless K8s Auth.

```
az aks update -n <cluster> -g <group> --enable-oidc-issuer
```

---

### 3.2 Restrict ServiceAccount Access
Each app should have its own ServiceAccount:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
```

Restrict namespace binding.

---

### 3.3 Enforce RBAC for Secret Access
ESO needs only minimal permissions:

```
Role:
- get
- list
- watch
- create secret
```

---

### 3.4 Turn on Secret Encryption at Rest in AKS

```
az aks update \
 --name aks-demo \
 --resource-group aks-rg \
 --enable-secret-rotation \
 --rotation-poll-interval 3m
```

---

## 4. Hardening Akeyless Configuration

### 4.1 Use Access Roles per Environment
Examples:
- `/prod/*`
- `/dev/*`
- `/shared/*`

Never reuse roles across namespaces or clusters.

---

### 4.2 Enable MFA for Privileged Users
Production teams must use MFA for:
- Creating auth methods  
- Granting access roles  
- Managing gateways  

---

### 4.3 Use Customer-Controlled Gateways
Gateways store cryptographic fragments locally.

```
Akeyless Gateway = Private Fragment Storage + Compliance Boundary
```

---

## 5. Best Practices for External Secrets Operator

### 5.1 Increase Refresh Interval for Prod
```
refreshInterval: 1m
```

### 5.2 Disable Unused Providers
Do not allow AWS, GCP, or HashiCorp if not required.

---

## 6. Secret Rotation Architecture

### 6.1 Akeyless-Driven Rotation
Akeyless rotates secrets → ESO updates K8s → Apps reload.

```
Akeyless → ESO → Kubernetes Secret → Pod
```

### 6.2 Rotation Types
- Static
- Dynamic
- Auto-rotated
- Just-in-time credentials

---

## 7. GitOps Best Practices

### 7.1 SecretStore and ExternalSecret Manifests must be in Git
Example structure:

```
cluster/
   prod/
     secretstore.yaml
     externalsecret-db.yaml
   dev/
     externalsecret-api.yaml
```

### 7.2 Use Kustomize per environment
Prevents mistakes across clusters.

---

## 8. Monitoring & Logging

Enable:
- Akeyless Audit Logs  
- AKS Container Insights  
- Kubernetes Events from ESO  

ESO events show:
- Sync success  
- Sync failure  
- Access Denied  
- JWT validation errors  

---

## 9. Compliance Considerations

Akeyless supports:
- SOC2  
- HIPAA  
- FedRAMP  
- ISO27001  

For enterprise:
- Use Gateway mode  
- Lock down egress  
- Enforce logging immutability  

---

## 10. Summary
You now understand:
- How to harden AKS  
- How to secure Akeyless access flows  
- Best practices to run ESO in production  
- Rotation patterns  
- Monitoring & compliance integration  
