# Week 3 — Decentralized Authentication

For force change password    
```sh
aws cognito-idp admin-set-user-password \    
  --user-pool-id <your-user-pool-id> \    
  --username <username> \
  --password <password> \
  --permanent   
```
