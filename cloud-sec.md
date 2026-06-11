SCP
 ↓
Permission Boundary
 ↓
Session Policy
 ↓
IAM Policy
 ↓
Resource Policy
 ↓
Explicit Deny Check

SCP
= AWS Organizations
= Root / OU / Account Level
= Maximum permissions for the account

Permission Boundary
= IAM User / IAM Role
= Maximum permissions for that identity

Session Policy
= STS AssumeRole / Identity Center
= Temporary restriction during login session

IAM Policy (Identity Policy)
= IAM User / Group / Role
= Actual permissions granted

Resource Policy
= Attached directly to resource
= S3 Bucket Policy
= KMS Key Policy
= SNS Topic Policy
= SQS Queue Policy
= Secrets Manager Policy

Explicit Deny
= Can exist in ANY of the above
= Always wins

Organization → SCP

Identity → Permission Boundary

Session → Session Policy

Role/User → IAM Policy

Resource → Resource Policy

Any Deny → DENY

``` SCP controls the account,
Permission Boundary controls the role,
Session Policy controls the session,
IAM Policy grants permissions,
Resource Policy grants resource access,
and Explicit Deny anywhere overrides everything.
```

