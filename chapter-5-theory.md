# Chapter 5 Theory



---

## Architecture Diagram


```mermaid
graph TD
  Err[Error: invalid JWT] --> Log[Check ESO Logs]
  Err --> Auth[Validate OIDC issuer]
  Err --> Role[Check Access Role]
```


---

Content missing.
