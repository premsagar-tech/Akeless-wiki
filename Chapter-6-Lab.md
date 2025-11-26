# Chapter 6 Lab – Multi-App, Multi-Environment Lab

## 1. Create namespaces
```
kubectl create ns dev
kubectl create ns prod
```

## 2. Create separate ServiceAccounts
```
kubectl create sa dev-sa -n dev
kubectl create sa prod-sa -n prod
```

## 3. Create Akeyless secrets
```
akeyless secret create --name /dev/app/password --value DevPass
akeyless secret create --name /prod/app/password --value ProdPass
```

## 4. Create ExternalSecrets for both
Apply dev + prod manifests.

## 5. Validate isolation
Dev pods cannot read prod secrets.
