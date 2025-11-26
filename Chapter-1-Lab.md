# Chapter 1 Lab – Understanding AKS + Akeyless + ESO (Foundational Lab)

## 🎯 Lab Objective
This lab helps you understand the concepts from Chapter 1 using **hands-on** exploratory tasks.  
You will not deploy anything yet—this lab is meant to build foundational understanding.

---

## 🧪 Lab Sections

### 1. Explore Kubernetes Secrets Behavior
**Objective:** Understand why Kubernetes Secrets are not secure by default.

**Steps:**
1. Create a test secret:
   ```
   kubectl create secret generic demo-secret --from-literal=password=MyTestPassword
   ```
2. Retrieve the secret:
   ```
   kubectl get secret demo-secret -o yaml
   ```
3. Decode the value:
   ```
   echo "<base64-string>" | base64 --decode
   ```
4. Observe:
   - Secret is only base64 encoded.
   - Anyone with access to the namespace can decode it.

---

### 2. Understand External Secrets Operator (ESO)
**Objective:** Discover how ESO works internally.

**Steps:**
1. Visit the ESO GitHub repo:
   https://external-secrets.io
2. Open the concepts section:
   - `ExternalSecret`
   - `SecretStore`
   - `ClusterSecretStore`
   - Refresh intervals
3. Draw (or understand) the ESO sync process:
   ```
   ExternalSecret → ESO Controller → Kubernetes Secret → Pod
   ```

---

### 3. Study Akeyless Architecture
**Objective:** Understand ZERO-KNOWLEDGE vaultless architecture.

**Steps:**
1. Read about DFC (Distributed Fragments Cryptography)
2. Learn:
   - Akeyless never stores whole secrets
   - Cryptographic fragments live in separate locations

---

### 4. Full Flow Review (Theory)
Open the architecture diagrams in the main chapter and ensure you understand:

- How ESO authenticates to Akeyless  
- How Akeyless verifies Kubernetes identity  
- How Kubernetes Secrets get updated  
- Why this system improves security  

---

## 📝 Lab Checkpoints
You should now be able to answer:

1. Why Kubernetes Secrets are insecure by default  
2. Why Akeyless is zero-knowledge  
3. How ESO syncs secrets  
4. End-to-end flow from Akeyless → ESO → Secret → Pod  

---

## ✅ Lab Complete
This lab builds conceptual readiness before doing the real deployment in Chapter 3.
