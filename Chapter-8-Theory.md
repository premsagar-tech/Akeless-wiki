# Chapter 8 – Production SOPs, Operations & Runbooks

## 1. Daily Operations SOP
- Check ESO controller logs  
- Check Akeyless audit logs  
- Verify ExternalSecret sync status  

## 2. Weekly SOP
- Rotate high-risk secrets  
- Validate namespace access boundary  

## 3. Monthly SOP
- RBAC audits  
- Gateway resource validation  

## 4. Runbook: ESO Sync Failure
Steps:
1. Check ESO logs  
2. Validate SecretStore  
3. Test Akeyless connectivity  
4. Re-run ExternalSecret  

## 5. Runbook: Akeyless Authentication Failure
1. Check OIDC issuer  
2. Validate JWT SA  
3. Validate AccessRole  
