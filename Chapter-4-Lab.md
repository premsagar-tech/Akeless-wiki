# Chapter 4 Lab – Production Hardening & Security Validations

## 🎯 Objective
Validate and implement production-ready security configurations for AKS + Akeyless + ESO.

---

## 1. Validate OIDC Issuer in AKS
```
az aks show -g aks-rg -n aks-demo --query "oidcIssuerProfile.issuerUrl"
```

---

## 2. Validate ServiceAccount Isolation
Create namespace:
```
kubectl create ns finance
```

Create ServiceAccount:
```
kubectl create sa finance-sa -n finance
kubectl describe sa finance-sa -n finance
```

Check token annotation:
```
kubectl get secret -n finance
```

---

## 3. Validate Akeyless Permissions
List roles:
```
akeyless access-role list
```

Check permissions:
```
akeyless access-role get --name aks-role
```

Ensure:
- No wildcard (`/*`) permissions
- No unnecessary paths

---

## 4. Validate ESO Sync Failures (Security Test)

1. Point SecretStore to invalid AccessID
2. Run:
```
kubectl describe externalsecret <name>
```

Verify:
- Errors show “Access Denied”
- ESO does NOT create secret

---

## 5. Validate Rotation Flow

### Step 1: Update Akeyless Secret
```
akeyless secret update --name /prod/db/password --value RotatedPass123
```

### Step 2: Check ESO Sync
```
kubectl describe externalsecret db-password
```

### Step 3: Validate K8s Secret
```
kubectl get secret db-secret -o yaml
```

You should see updated Base64 value.

---

## 6. Validate GitOps Sync Logic
If using ArgoCD:
```
argocd app sync prod-secrets
argocd app get prod-secrets
```

Check:
- ExternalSecret manifests applied correctly  
- No drift  

---

## 7. Validate Audit Logs in Akeyless

In Akeyless UI:
- Navigate to **Audit Logs**
- Search: `FetchSecret`, `AuthMethod`

You should see:
- Kubernetes auth requests
- Secret fetches
- Token issuance logs

---

## 8. Summary
You have:
- Validated OIDC  
- Checked service account identity  
- Verified Akeyless policies  
- Tested failed ESO sync (security boundary)  
- Observed live rotation  
- Conducted GitOps validation  
