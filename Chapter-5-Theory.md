# Chapter 5 – Troubleshooting, Failure Scenarios & Debug Guide

## 1. Introduction
This chapter covers **real-world troubleshooting** for AKS + Akeyless + ESO, focusing on:
- Authentication failures  
- Secret sync issues  
- RBAC misconfigurations  
- Rotation failures  
- GitOps conflicts  
- Network connectivity issues  

---

## 2. ESO Troubleshooting

### 2.1 Check ESO Logs
```
kubectl logs -n external-secrets deploy/eso-external-secrets
```

Typical errors:
- `AccessDenied`
- `invalid JWT`
- `cannot fetch secret`
- `SecretStore authentication failed`

---

### 2.2 Validate ExternalSecret Events
```
kubectl describe externalsecret <name>
```

Look for:
- Sync success events  
- Sync failures  
- Permission denies  

---

## 3. Akeyless Troubleshooting

### 3.1 Validate Auth Method Status
```
akeyless auth-method get --name aks-auth
```

Check:
- Issuer URL match  
- Audience  
- Bound namespaces  

---

### 3.2 Validate Secret Access
```
akeyless get-secret-value --name /path/key
```

---

## 4. Common Failure Scenarios

### Scenario 1 – JWT Validation Error
Cause:
- OIDC issuer incorrect
- ServiceAccount not bound

Fix:
1. Get issuer:
```
az aks show --query "oidcIssuerProfile.issuerUrl"
```
2. Update Akeyless auth method.

---

### Scenario 2 – ESO cannot fetch secret
Fix:
- Verify AccessID
- Check roles:
```
akeyless access-role get --name aks-role
```

---

### Scenario 3 – Rotation Not Updating in Kubernetes
Fix:
- Ensure refreshInterval < secret TTL  
- Check ESO controller logs  
- Validate ExternalSecret events  

---

### Scenario 4 – Pod not receiving updated secrets
Cause:
- Apps not watching secret reload

Fix:
- Enable secret reload sidecars
- Mount secrets as volumes instead of env vars

---

### Scenario 5 – Gateway Connectivity Down
Fix:
- Check gateway logs  
- Check firewall rules  
- Validate FRAGMENT storage reachable  

---

## 5. GitOps Troubleshooting

### 5.1 ArgoCD Sync Failures
```
argocd app get secrets-app
```

Look for:
- YAML schema errors  
- Missing CRDs  

---

### 5.2 Drift Detection
```
argocd app diff secrets-app
```

---

## 6. Network & Firewall Debugging

### 6.1 Validate Egress to Akeyless API
```
curl https://api.akeyless.io -v
```

---

### 6.2 AKS Network Policy
Ensure ESO namespace is allowed to egress.

---

## 7. Summary
You can now troubleshoot:
- ESO  
- AKS identity  
- Akeyless auth  
- GitOps integrations  
- Gateway connectivity  
