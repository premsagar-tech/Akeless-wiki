# Chapter 5 Lab – Failure Injection, Debugging & Recovery

## 🎯 Objective
Simulate real-world failure cases and validate recovery steps.

---

## Lab 1 – Break Akeyless Authentication

### Step 1: Modify AccessID to invalid value
Edit SecretStore:
```
accessID: "INVALID-ID"
```
Apply and observe:
```
kubectl describe externalsecret <name>
```

Expected:
- Access Denied  
- ESO cannot sync  

---

## Lab 2 – Break JWT Authentication

1. Update auth method to wrong issuer.
2. Run:
```
kubectl describe externalsecret <name>
```
Expected:
- JWT validation failed  

---

## Lab 3 – Break Akeyless Role Permissions

Remove secret path from access role:
```
akeyless access-role dissoc-target ...
```

Expected:
- FetchSecret failed  

---

## Lab 4 – Break Pod Secret Consumption

1. Delete Kubernetes Secret:
```
kubectl delete secret db-secret
```
2. Check ESO:
```
kubectl describe externalsecret db-password
```

Expected:
- ESO recreates secret  

---

## Lab 5 – GitOps Failure Case

1. Add syntax error in ExternalSecret YAML.
2. Commit and push.
3. Check ArgoCD:
```
argocd app sync
```

Expected:
- Sync failure  
- YAML validation error  

---

## Lab 6 – Network Failure Simulation

1. Block egress from ESO namespace.
2. Verify:
```
kubectl logs -n external-secrets <pod>
```

Expected:
- Connection timeout  
- Cannot reach Akeyless  

---

## Lab Complete
You have simulated:
- Auth failures  
- Permission denies  
- Network issues  
- GitOps sync failures  
