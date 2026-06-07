# Complete Interview Cracking Guide: AWS Security Engineer Role

Based on your resume and the JD, let me build a comprehensive, battle-tested guide. I'll cover every dimension — concepts, code, architecture, Q&A, and behavioral prep.

---

## 📋 Table of Contents
1. [JD Gap Analysis & Your Positioning](#gap-analysis)
2. [Core AWS Services Deep Dive](#aws-services)
3. [Python & Boto3 Mastery](#python-boto3)
4. [Serverless Workflows Architecture](#serverless)
5. [Security Automation Patterns](#security-automation)
6. [Interview Q&A Bank](#qa-bank)
7. [System Design Scenarios](#system-design)
8. [Behavioral STAR Stories](#behavioral)
9. [Coding Challenges](#coding-challenges)
10. [Final Preparation Checklist](#checklist)

---

## 1. JD Gap Analysis & Your Positioning {#gap-analysis}

### Strengths You Already Have (Highlight These Hard)
| JD Requirement | Your Evidence |
|---|---|
| AWS IAM, Organizations, Control Tower | Multi-account AFT implementation, 10+ accounts |
| Python + Boto3 | boto3 in skills, automation work |
| Serverless workflows | Lambda, API Gateway, EventBridge in skills |
| Shift-left security | SonarQube, Trivy, OWASP ZAP pipeline gates |
| Monitoring & alerting | Prometheus, Grafana, CloudWatch |
| CI/CD automation | GitLab, GitHub Actions, Azure DevOps |

### Gaps to Bridge (Prepare Extra Hard)
| JD Requirement | Gap | How to Answer |
|---|---|---|
| **AWS Identity Center (SSO)** | Not explicitly mentioned | Map to your Control Tower + AFT work |
| **EventBridge deep expertise** | Listed in skills but no project detail | Prepare 2 detailed scenarios |
| **Lambda production experience** | Listed but no project story | Build a story around security automation |
| **Boto3 debugging/optimization** | Need specific examples | Prepare pagination, retry, error handling code |

---

## 2. Core AWS Services Deep Dive {#aws-services}

### 2.1 AWS IAM — Complete Mental Model

```
┌─────────────────────────────────────────────────────────────┐
│                    IAM COMPLETE HIERARCHY                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  IDENTITY                    PERMISSION                     │
│  ─────────                   ──────────                     │
│  • Users                     • Policies (JSON)              │
│  • Groups                      - Identity-based             │
│  • Roles                       - Resource-based             │
│  • Identity Center             - Permission boundaries      │
│    (SSO Principals)            - SCPs (Org level)           │
│                                - Session policies           │
│                                                             │
│  AUTHENTICATION              AUTHORIZATION                  │
│  ──────────────              ─────────────                  │
│  • Password + MFA            • Policy evaluation logic      │
│  • Access Keys               • Explicit Deny wins           │
│  • STS AssumeRole            • Implicit Deny default        │
│  • OIDC Federation           • Resource-based + Identity    │
│  • SAML 2.0                    = Union (cross-account)      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### IAM Policy Evaluation Logic (CRITICAL — Always Asked)

```
┌──────────────────────────────────────────────────────────────┐
│              IAM POLICY EVALUATION ORDER                     │
│                                                              │
│  1. EXPLICIT DENY anywhere? ──► DENY (stops here)           │
│  2. SCP allows? ──► If NO ──► DENY                          │
│  3. Resource-based policy allows? ──► ALLOW (same account)  │
│  4. Permission boundary allows? ──► If NO ──► DENY          │
│  5. Identity-based policy allows? ──► ALLOW                 │
│  6. Session policy allows? ──► If NO ──► DENY               │
│  7. Default ──► IMPLICIT DENY                               │
│                                                              │
│  KEY RULE: Explicit Deny > Everything else                   │
│  KEY RULE: Cross-account = BOTH resource + identity needed   │
└──────────────────────────────────────────────────────────────┘
```

#### IAM Least Privilege — Production Pattern

```python
# PRODUCTION IAM POLICY — Least Privilege with Conditions
import boto3
import json

def create_least_privilege_policy(
    policy_name: str,
    service: str,
    actions: list,
    resource_arns: list,
    conditions: dict = None
) -> dict:
    """
    Creates a least-privilege IAM policy with optional conditions.
    Best practice: Always scope to specific resources, never use '*'
    """
    
    policy_document = {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": f"LeastPrivilege{service.capitalize()}Access",
                "Effect": "Allow",
                "Action": actions,
                "Resource": resource_arns,
                "Condition": conditions or {}
            }
        ]
    }
    
    # Remove empty condition block
    if not conditions:
        del policy_document["Statement"][0]["Condition"]
    
    iam = boto3.client('iam')
    
    try:
        response = iam.create_policy(
            PolicyName=policy_name,
            PolicyDocument=json.dumps(policy_document),
            Description=f"Least privilege policy for {service} - auto-generated"
        )
        return response['Policy']
    except iam.exceptions.EntityAlreadyExistsException:
        # Get existing policy ARN and create new version
        account_id = boto3.client('sts').get_caller_identity()['Account']
        policy_arn = f"arn:aws:iam::{account_id}:policy/{policy_name}"
        
        # Clean up old versions (max 5 allowed)
        _cleanup_policy_versions(iam, policy_arn)
        
        response = iam.create_policy_version(
            PolicyArn=policy_arn,
            PolicyDocument=json.dumps(policy_document),
            SetAsDefault=True
        )
        return {"PolicyArn": policy_arn, "VersionId": response['PolicyVersion']['VersionId']}


def _cleanup_policy_versions(iam_client, policy_arn: str):
    """Keep only the 4 most recent non-default versions (AWS limit is 5 total)"""
    versions = iam_client.list_policy_versions(PolicyArn=policy_arn)['Versions']
    non_default = [v for v in versions if not v['IsDefaultVersion']]
    
    # Sort by creation date, delete oldest if at limit
    non_default.sort(key=lambda x: x['CreateDate'])
    while len(non_default) >= 4:
        oldest = non_default.pop(0)
        iam_client.delete_policy_version(
            PolicyArn=policy_arn,
            VersionId=oldest['VersionId']
        )


# EXAMPLE USAGE — S3 read-only with MFA condition
policy = create_least_privilege_policy(
    policy_name="AppS3ReadOnlyPolicy",
    service="s3",
    actions=["s3:GetObject", "s3:ListBucket"],
    resource_arns=[
        "arn:aws:s3:::my-app-bucket",
        "arn:aws:s3:::my-app-bucket/*"
    ],
    conditions={
        "Bool": {"aws:MultiFactorAuthPresent": "true"},
        "StringEquals": {"s3:prefix": ["app-data/", "configs/"]}
    }
)
```

---

### 2.2 AWS Identity Center (SSO) — Deep Dive

```
┌─────────────────────────────────────────────────────────────────┐
│              AWS IDENTITY CENTER ARCHITECTURE                   │
│                                                                 │
│  ┌─────────────┐    SAML/OIDC    ┌──────────────────────────┐  │
│  │  External   │◄──────────────►│   AWS Identity Center    │  │
│  │  IdP        │                │   (Management Account)   │  │
│  │  (Okta/AD/  │                │                          │  │
│  │   Azure AD) │                │  ┌────────────────────┐  │  │
│  └─────────────┘                │  │  Permission Sets   │  │  │
│                                 │  │  (like IAM Roles)  │  │  │
│  ┌─────────────┐                │  └────────────────────┘  │  │
│  │  Built-in   │                │                          │  │
│  │  Directory  │                │  ┌────────────────────┐  │  │
│  └─────────────┘                │  │  Account           │  │  │
│                                 │  │  Assignments       │  │  │
│                                 │  └────────────────────┘  │  │
│                                 └──────────────────────────┘  │
│                                           │                    │
│                    ┌──────────────────────┼──────────────────┐ │
│                    ▼                      ▼                  ▼ │
│             ┌────────────┐        ┌────────────┐    ┌──────────┐│
│             │  Dev Acct  │        │ Prod Acct  │    │ Sec Acct ││
│             │  (Role     │        │ (Role      │    │ (Role    ││
│             │  assumed   │        │  assumed   │    │  assumed ││
│             │  via SSO)  │        │  via SSO)  │    │  via SSO)││
│             └────────────┘        └────────────┘    └──────────┘│
└─────────────────────────────────────────────────────────────────┘
```

```python
# BOTO3 — Identity Center Automation
import boto3
from typing import Optional

class IdentityCenterManager:
    """
    Manages AWS Identity Center (SSO) operations programmatically.
    Used for automated user provisioning, permission set management.
    """
    
    def __init__(self, region: str = 'us-east-1'):
        self.sso_admin = boto3.client('sso-admin', region_name=region)
        self.identity_store = boto3.client('identitystore', region_name=region)
        self.instance_arn, self.identity_store_id = self._get_instance_info()
    
    def _get_instance_info(self) -> tuple:
        """Get SSO instance ARN and Identity Store ID"""
        instances = self.sso_admin.list_instances()['Instances']
        if not instances:
            raise ValueError("No Identity Center instance found")
        instance = instances[0]
        return instance['InstanceArn'], instance['IdentityStoreId']
    
    def create_permission_set(
        self,
        name: str,
        description: str,
        session_duration: str = "PT8H",  # ISO 8601 duration
        managed_policies: list = None,
        inline_policy: dict = None
    ) -> str:
        """
        Creates a Permission Set — equivalent to an IAM Role template
        that gets instantiated in each assigned account.
        """
        import json
        
        response = self.sso_admin.create_permission_set(
            Name=name,
            Description=description,
            InstanceArn=self.instance_arn,
            SessionDuration=session_duration,
            Tags=[
                {'Key': 'ManagedBy', 'Value': 'automation'},
                {'Key': 'Environment', 'Value': 'all'}
            ]
        )
        
        permission_set_arn = response['PermissionSet']['PermissionSetArn']
        
        # Attach AWS managed policies
        if managed_policies:
            for policy_arn in managed_policies:
                self.sso_admin.attach_managed_policy_to_permission_set(
                    InstanceArn=self.instance_arn,
                    PermissionSetArn=permission_set_arn,
                    ManagedPolicyArn=policy_arn
                )
        
        # Attach inline policy for fine-grained control
        if inline_policy:
            self.sso_admin.put_inline_policy_to_permission_set(
                InstanceArn=self.instance_arn,
                PermissionSetArn=permission_set_arn,
                InlinePolicy=json.dumps(inline_policy)
            )
        
        return permission_set_arn
    
    def assign_user_to_account(
        self,
        username: str,
        account_id: str,
        permission_set_arn: str
    ) -> dict:
        """
        Assigns a user to an AWS account with a specific permission set.
        This is the core operation for access provisioning.
        """
        # Find user ID from username
        user_id = self._get_user_id(username)
        
        response = self.sso_admin.create_account_assignment(
            InstanceArn=self.instance_arn,
            TargetId=account_id,
            TargetType='AWS_ACCOUNT',
            PermissionSetArn=permission_set_arn,
            PrincipalType='USER',
            PrincipalId=user_id
        )
        
        # Poll for completion (async operation)
        return self._wait_for_assignment(
            response['AccountAssignmentCreationStatus']['RequestId']
        )
    
    def _get_user_id(self, username: str) -> str:
        """Find user ID by username in Identity Store"""
        response = self.identity_store.list_users(
            IdentityStoreId=self.identity_store_id,
            Filters=[{
                'AttributePath': 'UserName',
                'AttributeValue': username
            }]
        )
        users = response.get('Users', [])
        if not users:
            raise ValueError(f"User '{username}' not found in Identity Store")
        return users[0]['UserId']
    
    def _wait_for_assignment(self, request_id: str, max_attempts: int = 10) -> dict:
        """Poll assignment status with exponential backoff"""
        import time
        
        for attempt in range(max_attempts):
            response = self.sso_admin.describe_account_assignment_creation_status(
                InstanceArn=self.instance_arn,
                AccountAssignmentCreationRequestId=request_id
            )
            status = response['AccountAssignmentCreationStatus']
            
            if status['Status'] == 'SUCCEEDED':
                return status
            elif status['Status'] == 'FAILED':
                raise Exception(f"Assignment failed: {status.get('FailureReason')}")
            
            # Exponential backoff
            time.sleep(2 ** attempt)
        
        raise TimeoutError("Assignment did not complete in time")
    
    def list_account_assignments(self, account_id: str) -> list:
        """
        Audit function — list all assignments for an account.
        Critical for security reviews and access audits.
        """
        assignments = []
        paginator = self.sso_admin.get_paginator('list_account_assignments')
        
        # First get all permission sets for this account
        ps_paginator = self.sso_admin.get_paginator(
            'list_permission_sets_provisioned_to_account'
        )
        
        for ps_page in ps_paginator.paginate(
            InstanceArn=self.instance_arn,
            AccountId=account_id
        ):
            for ps_arn in ps_page.get('PermissionSets', []):
                for page in paginator.paginate(
                    InstanceArn=self.instance_arn,
                    AccountId=account_id,
                    PermissionSetArn=ps_arn
                ):
                    assignments.extend(page.get('AccountAssignments', []))
        
        return assignments
```

---

### 2.3 AWS Organizations & SCPs

```
┌──────────────────────────────────────────────────────────────────┐
│                  AWS ORGANIZATIONS HIERARCHY                     │
│                                                                  │
│                    ┌─────────────────┐                          │
│                    │  Root (OU)      │  ◄── SCPs apply here     │
│                    └────────┬────────┘      cascade down        │
│                             │                                    │
│          ┌──────────────────┼──────────────────┐                │
│          ▼                  ▼                  ▼                │
│   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐          │
│   │ Security OU │   │ Workload OU │   │ Sandbox OU  │          │
│   │             │   │             │   │             │          │
│   │ • Log Arch  │   │ • Dev OU    │   │ • No prod   │          │
│   │ • Audit     │   │ • Staging   │   │   data      │          │
│   │ • Security  │   │ • Prod OU   │   │ • Relaxed   │          │
│   │   Tooling   │   │             │   │   SCPs      │          │
│   └─────────────┘   └─────────────┘   └─────────────┘          │
│                                                                  │
│  SCP INHERITANCE: Root SCP → OU SCP → Account SCP               │
│  EFFECTIVE POLICY = Intersection of all SCPs in hierarchy        │
└──────────────────────────────────────────────────────────────────┘
```

```python
# SCP AUTOMATION — Deny Dangerous Actions
import boto3
import json

class SCPManager:
    """
    Manages Service Control Policies for AWS Organizations.
    SCPs are guardrails — they define the MAXIMUM permissions
    any principal in the OU/account can have.
    """
    
    def __init__(self):
        self.org_client = boto3.client('organizations')
    
    def create_security_guardrail_scp(self) -> str:
        """
        Creates a comprehensive security guardrail SCP.
        This is a DENY-list SCP — denies dangerous operations
        regardless of what IAM policies allow.
        """
        
        scp_policy = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "DenyRootAccountUsage",
                    "Effect": "Deny",
                    "Action": "*",
                    "Resource": "*",
                    "Condition": {
                        "StringLike": {
                            "aws:PrincipalArn": "arn:aws:iam::*:root"
                        }
                    }
                },
                {
                    "Sid": "DenyLeavingOrganization",
                    "Effect": "Deny",
                    "Action": "organizations:LeaveOrganization",
                    "Resource": "*"
                },
                {
                    "Sid": "DenyDisablingSecurityServices",
                    "Effect": "Deny",
                    "Action": [
                        "cloudtrail:DeleteTrail",
                        "cloudtrail:StopLogging",
                        "cloudtrail:UpdateTrail",
                        "guardduty:DeleteDetector",
                        "guardduty:DisassociateFromMasterAccount",
                        "securityhub:DisableSecurityHub",
                        "config:DeleteConfigurationRecorder",
                        "config:StopConfigurationRecorder"
                    ],
                    "Resource": "*",
                    "Condition": {
                        "ArnNotLike": {
                            "aws:PrincipalArn": [
                                "arn:aws:iam::*:role/SecurityBreakGlassRole",
                                "arn:aws:iam::*:role/AWSControlTowerExecution"
                            ]
                        }
                    }
                },
                {
                    "Sid": "DenyRegionsOutsideApproved",
                    "Effect": "Deny",
                    "NotAction": [
                        "iam:*",
                        "organizations:*",
                        "route53:*",
                        "budgets:*",
                        "waf:*",
                        "cloudfront:*",
                        "sts:*",
                        "support:*",
                        "trustedadvisor:*"
                    ],
                    "Resource": "*",
                    "Condition": {
                        "StringNotEquals": {
                            "aws:RequestedRegion": [
                                "us-east-1",
                                "us-west-2",
                                "ap-south-1"
                            ]
                        }
                    }
                },
                {
                    "Sid": "RequireIMDSv2",
                    "Effect": "Deny",
                    "Action": "ec2:RunInstances",
                    "Resource": "arn:aws:ec2:*:*:instance/*",
                    "Condition": {
                        "StringNotEquals": {
                            "ec2:MetadataHttpTokens": "required"
                        }
                    }
                },
                {
                    "Sid": "DenyPublicS3Buckets",
                    "Effect": "Deny",
                    "Action": [
                        "s3:PutBucketPublicAccessBlock",
                        "s3:DeletePublicAccessBlock"
                    ],
                    "Resource": "*",
                    "Condition": {
                        "StringNotEquals": {
                            "s3:PublicAccessBlockConfiguration": {
                                "BlockPublicAcls": "true",
                                "BlockPublicPolicy": "true",
                                "IgnorePublicAcls": "true",
                                "RestrictPublicBuckets": "true"
                            }
                        }
                    }
                }
            ]
        }
        
        response = self.org_client.create_policy(
            Content=json.dumps(scp_policy),
            Description="Security guardrail SCP — denies dangerous operations",
            Name="SecurityGuardrailSCP",
            Type="SERVICE_CONTROL_POLICY"
        )
        
        return response['Policy']['PolicySummary']['Id']
    
    def attach_scp_to_ou(self, policy_id: str, target_id: str):
        """Attach SCP to an OU or account"""
        self.org_client.attach_policy(
            PolicyId=policy_id,
            TargetId=target_id
        )
    
    def audit_scp_coverage(self) -> dict:
        """
        Audit which OUs/accounts have SCPs attached.
        Returns coverage report for compliance.
        """
        report = {}
        
        # Get all OUs
        paginator = self.org_client.get_paginator('list_organizational_units_for_parent')
        roots = self.org_client.list_roots()['Roots']
        
        for root in roots:
            self._audit_ou_recursive(root['Id'], report)
        
        return report
    
    def _audit_ou_recursive(self, parent_id: str, report: dict):
        """Recursively audit OU tree"""
        # Get policies for this OU/account
        policies = self.org_client.list_policies_for_target(
            TargetId=parent_id,
            Filter='SERVICE_CONTROL_POLICY'
        )['Policies']
        
        report[parent_id] = {
            'scp_count': len(policies),
            'scps': [p['Name'] for p in policies]
        }
        
        # Recurse into child OUs
        try:
            paginator = self.org_client.get_paginator(
                'list_organizational_units_for_parent'
            )
            for page in paginator.paginate(ParentId=parent_id):
                for ou in page['OrganizationalUnits']:
                    self._audit_ou_recursive(ou['Id'], report)
        except Exception:
            pass  # Leaf node (account), not an OU
```

---

### 2.4 AWS Lambda — Production Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│                  LAMBDA EXECUTION MODEL                          │
│                                                                  │
│  COLD START:                                                     │
│  Download Code → Start Container → Init Runtime → Init Handler  │
│  (100ms - 10s)                                                   │
│                                                                  │
│  WARM START:                                                     │
│  Reuse Container → Execute Handler                               │
│  (< 10ms overhead)                                               │
│                                                                  │
│  OPTIMIZATION STRATEGIES:                                        │
│  • Provisioned Concurrency — pre-warm containers                 │
│  • Lambda SnapStart (Java) — snapshot after init                 │
│  • Minimize package size — layers for shared deps                │
│  • Move heavy init outside handler — reuse across invocations    │
│  • Use ARM64 (Graviton2) — 20% cheaper, often faster            │
│                                                                  │
│  CONCURRENCY MODEL:                                              │
│  • Reserved Concurrency — cap max, guarantee minimum            │
│  • Provisioned Concurrency — pre-initialized instances          │
│  • Account limit: 1000 concurrent (soft, can increase)          │
└──────────────────────────────────────────────────────────────────┘
```

```python
# PRODUCTION LAMBDA — Security Automation Handler
# This is the pattern they want to see

import boto3
import json
import logging
import os
from datetime import datetime, timezone
from typing import Any, Dict
from functools import lru_cache

# ─── BEST PRACTICE: Initialize clients OUTSIDE handler ───
# This reuses connections across warm invocations
logger = logging.getLogger()
logger.setLevel(logging.INFO)

# Lazy-loaded clients (initialized once per container)
_iam_client = None
_sns_client = None
_securityhub_client = None

def get_iam_client():
    global _iam_client
    if _iam_client is None:
        _iam_client = boto3.client('iam')
    return _iam_client

def get_sns_client():
    global _sns_client
    if _sns_client is None:
        _sns_client = boto3.client('sns')
    return _sns_client


# ─── MAIN HANDLER ───
def lambda_handler(event: Dict[str, Any], context) -> Dict[str, Any]:
    """
    Security automation Lambda — triggered by EventBridge rules.
    Detects and remediates IAM policy violations automatically.
    
    Trigger: EventBridge rule on CloudTrail events
    Actions: Detect overly permissive policies, alert, optionally remediate
    """
    
    logger.info(f"Event received: {json.dumps(event)}")
    logger.info(f"Request ID: {context.aws_request_id}")
    logger.info(f"Remaining time: {context.get_remaining_time_in_millis()}ms")
    
    try:
        # Parse CloudTrail event from EventBridge
        detail = event.get('detail', {})
        event_name = detail.get('eventName', '')
        user_identity = detail.get('userIdentity', {})
        request_params = detail.get('requestParameters', {})
        
        result = {
            'statusCode': 200,
            'event_name': event_name,
            'processed_at': datetime.now(timezone.utc).isoformat(),
            'actions_taken': []
        }
        
        # Route to appropriate handler
        if event_name in ['CreatePolicy', 'CreatePolicyVersion', 'PutUserPolicy', 
                          'PutRolePolicy', 'PutGroupPolicy']:
            result['actions_taken'] = handle_policy_creation(
                event_name, user_identity, request_params
            )
        
        elif event_name in ['AttachUserPolicy', 'AttachRolePolicy', 'AttachGroupPolicy']:
            result['actions_taken'] = handle_policy_attachment(
                event_name, user_identity, request_params
            )
        
        elif event_name == 'CreateAccessKey':
            result['actions_taken'] = handle_access_key_creation(
                user_identity, request_params
            )
        
        logger.info(f"Processing complete: {json.dumps(result)}")
        return result
        
    except Exception as e:
        logger.error(f"Error processing event: {str(e)}", exc_info=True)
        # Send alert but don't re-raise — prevents Lambda retry storms
        send_alert(
            subject="Lambda Security Automation Error",
            message=f"Error: {str(e)}\nEvent: {json.dumps(event)}"
        )
        return {'statusCode': 500, 'error': str(e)}


def handle_policy_creation(event_name: str, user_identity: dict, 
                           request_params: dict) -> list:
    """
    Analyzes newly created IAM policies for dangerous permissions.
    Detects: wildcard actions, wildcard resources, privilege escalation paths.
    """
    actions_taken = []
    
    policy_document = request_params.get('policyDocument', '{}')
    if isinstance(policy_document, str):
        policy_document = json.loads(policy_document)
    
    violations = analyze_policy_for_violations(policy_document)
    
    if violations:
        # Determine severity
        severity = 'CRITICAL' if any(v['type'] == 'WILDCARD_STAR' for v in violations) \
                   else 'HIGH'
        
        # Always alert
        send_alert(
            subject=f"[{severity}] Dangerous IAM Policy Detected",
            message=format_violation_alert(user_identity, violations, request_params)
        )
        actions_taken.append(f"Alert sent for {len(violations)} violations")
        
        # Auto-remediate only CRITICAL in non-prod
        if severity == 'CRITICAL' and should_auto_remediate(user_identity):
            remediate_policy(request_params, violations)
            actions_taken.append("Auto-remediation applied")
    
    return actions_taken


def analyze_policy_for_violations(policy_document: dict) -> list:
    """
    Core policy analysis engine.
    Checks for OWASP-style IAM misconfigurations.
    """
    violations = []
    statements = policy_document.get('Statement', [])
    
    if isinstance(statements, dict):
        statements = [statements]
    
    for stmt in statements:
        if stmt.get('Effect') != 'Allow':
            continue
        
        actions = stmt.get('Action', [])
        resources = stmt.get('Resource', [])
        
        if isinstance(actions, str):
            actions = [actions]
        if isinstance(resources, str):
            resources = [resources]
        
        # Check 1: Wildcard action on wildcard resource (Admin equivalent)
        if '*' in actions and '*' in resources:
            violations.append({
                'type': 'WILDCARD_STAR',
                'severity': 'CRITICAL',
                'description': 'Action "*" on Resource "*" — equivalent to AdministratorAccess',
                'statement': stmt
            })
        
        # Check 2: Privilege escalation actions
        priv_esc_actions = [
            'iam:CreatePolicyVersion', 'iam:SetDefaultPolicyVersion',
            'iam:PassRole', 'iam:AttachUserPolicy', 'iam:AttachRolePolicy',
            'sts:AssumeRole', 'lambda:CreateFunction', 'lambda:InvokeFunction'
        ]
        
        dangerous = [a for a in actions if a in priv_esc_actions or a == 'iam:*']
        if dangerous and '*' in resources:
            violations.append({
                'type': 'PRIVILEGE_ESCALATION_RISK',
                'severity': 'HIGH',
                'description': f'Privilege escalation actions on wildcard resource: {dangerous}',
                'statement': stmt
            })
        
        # Check 3: Data exfiltration risk
        exfil_actions = ['s3:GetObject', 's3:ListBucket', 'secretsmanager:GetSecretValue',
                         'ssm:GetParameter', 'kms:Decrypt']
        if any(a in exfil_actions for a in actions) and '*' in resources:
            violations.append({
                'type': 'DATA_EXFILTRATION_RISK',
                'severity': 'HIGH',
                'description': f'Sensitive data access on wildcard resource',
                'statement': stmt
            })
    
    return violations


def should_auto_remediate(user_identity: dict) -> bool:
    """
    Determines if auto-remediation should be applied.
    Only remediate in non-production accounts and for non-break-glass roles.
    """
    principal_arn = user_identity.get('arn', '')
    
    # Never auto-remediate break-glass or admin roles
    exempt_patterns = ['BreakGlass', 'SecurityAdmin', 'AWSControlTower']
    if any(pattern in principal_arn for pattern in exempt_patterns):
        return False
    
    # Check account tag for environment
    account_id = user_identity.get('accountId', '')
    return is_non_production_account(account_id)


@lru_cache(maxsize=128)
def is_non_production_account(account_id: str) -> bool:
    """Cached check — is this account tagged as non-production?"""
    try:
        org = boto3.client('organizations')
        tags = org.list_tags_for_resource(ResourceId=account_id)['Tags']
        env_tag = next((t['Value'] for t in tags if t['Key'] == 'Environment'), 'unknown')
        return env_tag.lower() not in ['production', 'prod']
    except Exception:
        return False  # Fail safe — don't remediate if unsure


def send_alert(subject: str, message: str):
    """Send SNS alert to security team"""
    sns_topic_arn = os.environ.get('SECURITY_ALERTS_SNS_TOPIC_ARN')
    if not sns_topic_arn:
        logger.warning("SNS_TOPIC_ARN not configured — alert not sent")
        return
    
    get_sns_client().publish(
        TopicArn=sns_topic_arn,
        Subject=subject[:100],  # SNS subject limit
        Message=message
    )


def format_violation_alert(user_identity: dict, violations: list, 
                           request_params: dict) -> str:
    """Format a human-readable alert message"""
    return f"""
SECURITY ALERT: IAM Policy Violation Detected
{'='*50}
Principal: {user_identity.get('arn', 'Unknown')}
Account: {user_identity.get('accountId', 'Unknown')}
Time: {datetime.now(timezone.utc).isoformat()}

VIOLATIONS FOUND ({len(violations)}):
{chr(10).join(f"  [{v['severity']}] {v['type']}: {v['description']}" for v in violations)}

Policy Name: {request_params.get('policyName', 'Unknown')}

ACTION REQUIRED: Review and remediate immediately.
"""
```

---

### 2.5 AWS EventBridge — Complete Patterns

```
┌──────────────────────────────────────────────────────────────────┐
│                  EVENTBRIDGE ARCHITECTURE                        │
│                                                                  │
│  EVENT SOURCES:                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │CloudTrail│  │GuardDuty │  │ Config   │  │ Custom   │        │
│  │ Events   │  │ Findings │  │ Changes  │  │  Apps    │        │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘        │
│       └─────────────┴─────────────┴─────────────┘              │
│                              │                                   │
│                    ┌─────────▼──────────┐                       │
│                    │   EVENT BUS        │                       │
│                    │  (default/custom)  │                       │
│                    └─────────┬──────────┘                       │
│                              │                                   │
│              ┌───────────────┼───────────────┐                  │
│              │               │               │                  │
│    ┌─────────▼──┐  ┌─────────▼──┐  ┌────────▼───┐             │
│    │   RULE 1   │  │   RULE 2   │  │   RULE 3   │             │
│    │ (pattern   │  │ (schedule  │  │ (pattern   │             │
│    │  match)    │  │  cron)     │  │  match)    │             │
│    └─────┬──────┘  └─────┬──────┘  └─────┬──────┘             │
│          │               │               │                      │
│    ┌─────▼──┐      ┌─────▼──┐      ┌─────▼──┐                 │
│    │Lambda  │      │Step    │      │ SNS/   │                  │
│    │        │      │Func    │      │ SQS    │                  │
│    └────────┘      └────────┘      └────────┘                  │
└──────────────────────────────────────────────────────────────────┘
```

```python
# EVENTBRIDGE RULE AUTOMATION — Security Monitoring
import boto3
import json

class EventBridgeSecurityRules:
    """
    Creates EventBridge rules for security monitoring.
    These rules trigger Lambda functions for automated response.
    """
    
    def __init__(self, region: str = 'us-east-1'):
        self.events_client = boto3.client('events', region_name=region)
        self.lambda_client = boto3.client('lambda', region_name=region)
    
    def create_iam_security_rule(self, lambda_arn: str) -> str:
        """
        Rule: Trigger on dangerous IAM operations via CloudTrail.
        Pattern matches specific API calls that indicate security risk.
        """
        
        # EventBridge pattern — matches CloudTrail events
        event_pattern = {
            "source": ["aws.iam"],
            "detail-type": ["AWS API Call via CloudTrail"],
            "detail": {
                "eventSource": ["iam.amazonaws.com"],
                "eventName": [
                    "CreatePolicy",
                    "CreatePolicyVersion", 
                    "AttachUserPolicy",
                    "AttachRolePolicy",
                    "PutUserPolicy",
                    "PutRolePolicy",
                    "CreateAccessKey",
                    "UpdateAccessKey",
                    "CreateLoginProfile",
                    "UpdateLoginProfile"
                ],
                "errorCode": [{"exists": False}]  # Only successful calls
            }
        }
        
        rule_name = "SecurityMonitor-IAMChanges"
        
        self.events_client.put_rule(
            Name=rule_name,
            EventPattern=json.dumps(event_pattern),
            State='ENABLED',
            Description='Monitors dangerous IAM operations for security automation',
            Tags=[
                {'Key': 'Purpose', 'Value': 'SecurityAutomation'},
                {'Key': 'ManagedBy', 'Value': 'SecurityTeam'}
            ]
        )
        
        # Add Lambda as target
        self.events_client.put_targets(
            Rule=rule_name,
            Targets=[{
                'Id': 'SecurityLambdaTarget',
                'Arn': lambda_arn,
                'RetryPolicy': {
                    'MaximumRetryAttempts': 3,
                    'MaximumEventAgeInSeconds': 3600
                },
                'DeadLetterConfig': {
                    'Arn': self._get_or_create_dlq()
                }
            }]
        )
        
        # Grant EventBridge permission to invoke Lambda
        self._grant_lambda_invoke_permission(lambda_arn, rule_name)
        
        return rule_name
    
    def create_guardduty_response_rule(self, lambda_arn: str, 
                                       severity_threshold: float = 7.0) -> str:
        """
        Rule: Auto-respond to high-severity GuardDuty findings.
        Severity: 0-10 scale. 7+ = High, 9+ = Critical
        """
        
        event_pattern = {
            "source": ["aws.guardduty"],
            "detail-type": ["GuardDuty Finding"],
            "detail": {
                "severity": [{"numeric": [">=", severity_threshold]}],
                "type": [
                    {"prefix": "UnauthorizedAccess:"},
                    {"prefix": "Backdoor:"},
                    {"prefix": "CryptoCurrency:"},
                    {"prefix": "Trojan:"},
                    {"prefix": "Recon:"}
                ]
            }
        }
        
        rule_name = "SecurityMonitor-GuardDutyHighSeverity"
        
        self.events_client.put_rule(
            Name=rule_name,
            EventPattern=json.dumps(event_pattern),
            State='ENABLED',
            Description=f'Responds to GuardDuty findings with severity >= {severity_threshold}'
        )
        
        self.events_client.put_targets(
            Rule=rule_name,
            Targets=[{
                'Id': 'GuardDutyResponseLambda',
                'Arn': lambda_arn
            }]
        )
        
        self._grant_lambda_invoke_permission(lambda_arn, rule_name)
        return rule_name
    
    def create_scheduled_compliance_rule(self, lambda_arn: str,
                                          schedule: str = "cron(0 6 * * ? *)") -> str:
        """
        Scheduled rule: Daily compliance check at 6 AM UTC.
        Runs security posture assessment across all accounts.
        """
        
        rule_name = "SecurityMonitor-DailyComplianceCheck"
        
        self.events_client.put_rule(
            Name=rule_name,
            ScheduleExpression=schedule,
            State='ENABLED',
            Description='Daily security compliance check'
        )
        
        self.events_client.put_targets(
            Rule=rule_name,
            Targets=[{
                'Id': 'ComplianceLambda',
                'Arn': lambda_arn,
                'Input': json.dumps({
                    'check_type': 'daily_compliance',
                    'checks': [
                        'mfa_enabled',
                        'access_key_rotation',
                        'unused_credentials',
                        's3_public_access',
                        'security_groups_open'
                    ]
                })
            }]
        )
        
        self._grant_lambda_invoke_permission(lambda_arn, rule_name)
        return rule_name
    
    def _grant_lambda_invoke_permission(self, lambda_arn: str, rule_name: str):
        """Grant EventBridge permission to invoke the Lambda function"""
        try:
            self.lambda_client.add_permission(
                FunctionName=lambda_arn,
                StatementId=f"EventBridge-{rule_name}",
                Action='lambda:InvokeFunction',
                Principal='events.amazonaws.com',
                SourceArn=f"arn:aws:events:{boto3.session.Session().region_name}:"
                          f"{boto3.client('sts').get_caller_identity()['Account']}:"
                          f"rule/{rule_name}"
            )
        except self.lambda_client.exceptions.ResourceConflictException:
            pass  # Permission already exists
    
    def _get_or_create_dlq(self) -> str:
        """Get or create Dead Letter Queue for failed event processing"""
        sqs = boto3.client('sqs')
        queue_name = "security-events-dlq"
        
        try:
            response = sqs.get_queue_url(QueueName=queue_name)
            attrs = sqs.get_queue_attributes(
                QueueUrl=response['QueueUrl'],
                AttributeNames=['QueueArn']
            )
            return attrs['Attributes']['QueueArn']
        except sqs.exceptions.QueueDoesNotExist:
            response = sqs.create_queue(
                QueueName=queue_name,
                Attributes={
                    'MessageRetentionPeriod': '1209600',  # 14 days
                    'KmsMasterKeyId': 'alias/aws/sqs'
                }
            )
            attrs = sqs.get_queue_attributes(
                QueueUrl=response['QueueUrl'],
                AttributeNames=['QueueArn']
            )
            return attrs['Attributes']['QueueArn']
```

---

## 3. Python & Boto3 Mastery {#python-boto3}

### 3.1 Boto3 Pagination — Critical Pattern

```python
# WRONG WAY — Misses results beyond first page
def get_all_users_wrong():
    iam = boto3.client('iam')
    response = iam.list_users()  # Only returns first 100!
    return response['Users']


# RIGHT WAY — Using paginator
def get_all_users_correct():
    iam = boto3.client('iam')
    paginator = iam.get_paginator('list_users')
    
    all_users = []
    for page in paginator.paginate():
        all_users.extend(page['Users'])
    
    return all_users


# ADVANCED — Paginator with filtering and transformation
def get_users_with_old_access_keys(days_threshold: int = 90) -> list:
    """
    Find all IAM users with access keys older than threshold.
    Production-grade: handles pagination, error handling, logging.
    """
    from datetime import datetime, timezone, timedelta
    
    iam = boto3.client('iam')
    threshold_date = datetime.now(timezone.utc) - timedelta(days=days_threshold)
    
    risky_users = []
    user_paginator = iam.get_paginator('list_users')
    
    for user_page in user_paginator.paginate():
        for user in user_page['Users']:
            try:
                key_paginator = iam.get_paginator('list_access_keys')
                
                for key_page in key_paginator.paginate(UserName=user['UserName']):
                    for key in key_page['AccessKeyMetadata']:
                        if (key['Status'] == 'Active' and 
                                key['CreateDate'] < threshold_date):
                            
                            # Get last used info
                            last_used = iam.get_access_key_last_used(
                                AccessKeyId=key['AccessKeyId']
                            )['AccessKeyLastUsed']
                            
                            risky_users.append({
                                'username': user['UserName'],
                                'user_arn': user['Arn'],
                                'access_key_id': key['AccessKeyId'],
                                'key_age_days': (datetime.now(timezone.utc) - 
                                                key['CreateDate']).days,
                                'last_used': last_used.get('LastUsedDate', 'Never'),
                                'last_used_service': last_used.get('ServiceName', 'N/A'),
                                'last_used_region': last_used.get('Region', 'N/A')
                            })
            
            except Exception as e:
                logger.error(f"Error checking user {user['UserName']}: {e}")
                continue
    
    return sorted(risky_users, key=lambda x: x['key_age_days'], reverse=True)
```

### 3.2 Boto3 Error Handling — Production Pattern

```python
import boto3
from botocore.exceptions import (
    ClientError, 
    BotoCoreError,
    EndpointConnectionError,
    NoCredentialsError
)
import time
import logging
from functools import wraps
from typing import Callable, Any

logger = logging.getLogger(__name__)


def aws_retry(
    max_attempts: int = 3,
    retryable_errors: list = None,
    backoff_base: float = 2.0
):
    """
    Decorator for retrying AWS API calls with exponential backoff.
    Handles throttling (ThrottlingException) and transient errors.
    """
    if retryable_errors is None:
        retryable_errors = [
            'ThrottlingException',
            'RequestLimitExceeded', 
            'ServiceUnavailable',
            'InternalError',
            'RequestTimeout'
        ]
    
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            last_exception = None
            
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                
                except ClientError as e:
                    error_code = e.response['Error']['Code']
                    error_message = e.response['Error']['Message']
                    
                    if error_code in retryable_errors:
                        wait_time = backoff_base ** attempt
                        logger.warning(
                            f"Retryable error '{error_code}' on attempt {attempt + 1}/"
                            f"{max_attempts}. Waiting {wait_time}s. Error: {error_message}"
                        )
                        time.sleep(wait_time)
                        last_exception = e
                    else:
                        # Non-retryable — raise immediately
                        logger.error(f"Non-retryable AWS error: {error_code}: {error_message}")
                        raise
                
                except EndpointConnectionError as e:
                    logger.error(f"Cannot connect to AWS endpoint: {e}")
                    raise
                
                except NoCredentialsError:
                    logger.error("No AWS credentials found")
                    raise
            
            logger.error(f"All {max_attempts} attempts failed")
            raise last_exception
        
        return wrapper
    return decorator


# USAGE EXAMPLE
@aws_retry(max_attempts=3)
def get_secret(secret_name: str, region: str = 'us-east-1') -> dict:
    """
    Retrieve secret from AWS Secrets Manager with retry logic.
    Production pattern: always use Secrets Manager, never hardcode credentials.
    """
    import json
    
    client = boto3.client('secretsmanager', region_name=region)
    
    try:
        response = client.get_secret_value(SecretId=secret_name)
        
        if 'SecretString' in response:
            return json.loads(response['SecretString'])
        else:
            # Binary secret
            import base64
            return base64.b64decode(response['SecretBinary'])
    
    except ClientError as e:
        error_code = e.response['Error']['Code']
        
        if error_code == 'ResourceNotFoundException':
            raise ValueError(f"Secret '{secret_name}' not found")
        elif error_code == 'InvalidRequestException':
            raise ValueError(f"Invalid request for secret '{secret_name}': {e}")
        elif error_code == 'AccessDeniedException':
            raise PermissionError(f"Access denied to secret '{secret_name}'")
        else:
            raise  # Let retry decorator handle it
```

### 3.3 Cross-Account Boto3 Operations

```python
# CROSS-ACCOUNT OPERATIONS — Critical for multi-account environments
import boto3
from contextlib import contextmanager
from typing import Generator

@contextmanager
def assume_role_session(
    role_arn: str,
    session_name: str = "AutomationSession",
    duration_seconds: int = 3600,
    external_id: str = None
) -> Generator[boto3.Session, None, None]:
    """
    Context manager for cross-account operations via STS AssumeRole.
    
    Usage:
        with assume_role_session("arn:aws:iam::123456789:role/SecurityAuditRole") as session:
            iam = session.client('iam')
            users = iam.list_users()
    """
    sts = boto3.client('sts')
    
    assume_role_kwargs = {
        'RoleArn': role_arn,
        'RoleSessionName': session_name,
        'DurationSeconds': duration_seconds
    }
    
    if external_id:
        assume_role_kwargs['ExternalId'] = external_id
    
    try:
        response = sts.assume_role(**assume_role_kwargs)
        credentials = response['Credentials']
        
        session = boto3.Session(
            aws_access_key_id=credentials['AccessKeyId'],
            aws_secret_access_key=credentials['SecretAccessKey'],
            aws_session_token=credentials['SessionToken']
        )
        
        yield session
        
    except Exception as e:
        logger.error(f"Failed to assume role {role_arn}: {e}")
        raise


def audit_all_accounts(org_role_name: str = "SecurityAuditRole") -> dict:
    """
    Runs security audit across ALL accounts in the organization.
    Uses Organizations API to discover accounts, then assumes role in each.
    """
    org = boto3.client('organizations')
    sts = boto3.client('sts')
    
    # Get current account ID to skip management account
    current_account = sts.get_caller_identity()['Account']
    
    audit_results = {}
    
    # Paginate through all accounts
    paginator = org.get_paginator('list_accounts')
    
    for page in paginator.paginate():
        for account in page['Accounts']:
            account_id = account['Id']
            account_name = account['Name']
            
            if account['Status'] != 'ACTIVE':
                continue
            
            role_arn = f"arn:aws:iam::{account_id}:role/{org_role_name}"
            
            try:
                with assume_role_session(
                    role_arn=role_arn,
                    session_name=f"SecurityAudit-{account_id}"
                ) as session:
                    
                    audit_results[account_id] = {
                        'account_name': account_name,
                        'findings': run_account_security_checks(session, account_id)
                    }
                    
                    logger.info(f"Audited account {account_name} ({account_id})")
            
            except Exception as e:
                logger.error(f"Failed to audit account {account_id}: {e}")
                audit_results[account_id] = {
                    'account_name': account_name,
                    'error': str(e)
                }
    
    return audit_results


def run_account_security_checks(session: boto3.Session, account_id: str) -> list:
    """Run security checks in a specific account"""
    findings = []
    
    iam = session.client('iam')
    
    # Check 1: Root account MFA
    try:
        summary = iam.get_account_summary()['SummaryMap']
        if summary.get('AccountMFAEnabled', 0) == 0:
            findings.append({
                'check': 'root_mfa',
                'severity': 'CRITICAL',
                'description': 'Root account MFA is not enabled'
            })
    except Exception as e:
        findings.append({'check': 'root_mfa', 'error': str(e)})
    
    # Check 2: Password policy
    try:
        iam.get_account_password_policy()
    except iam.exceptions.NoSuchEntityException:
        findings.append({
            'check': 'password_policy',
            'severity': 'HIGH',
            'description': 'No IAM password policy configured'
        })
    
    # Check 3: Users without MFA
    users_without_mfa = []
    paginator = iam.get_paginator('list_users')
    
    for page in paginator.paginate():
        for user in page['Users']:
            mfa_devices = iam.list_mfa_devices(UserName=user['UserName'])['MFADevices']
            if not mfa_devices:
                users_without_mfa.append(user['UserName'])
    
    if users_without_mfa:
        findings.append({
            'check': 'user_mfa',
            'severity': 'HIGH',
            'description': f'{len(users_without_mfa)} users without MFA: {users_without_mfa[:5]}'
        })
    
    return findings
```

---

## 4. Serverless Workflows Architecture {#serverless}

### 4.1 Complete Serverless Security Automation Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│           SERVERLESS SECURITY AUTOMATION ARCHITECTURE                   │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    EVENT SOURCES                                 │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │   │
│  │  │CloudTrail│  │GuardDuty │  │  Config  │  │  Scheduled   │   │   │
│  │  │  (S3 +   │  │ Findings │  │  Rules   │  │  (EventBridge│   │   │
│  │  │  CW Logs)│  │          │  │          │  │   cron)      │   │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └──────┬───────┘   │   │
│  └───────┼─────────────┼─────────────┼────────────────┼───────────┘   │
│          └─────────────┴─────────────┴────────────────┘               │
│                                    │                                    │
│                    ┌───────────────▼───────────────┐                   │
│                    │      EVENTBRIDGE BUS           │                   │
│                    │   (Security Event Router)      │                   │
│                    └───────────────┬───────────────┘                   │
│                                    │                                    │
│          ┌─────────────────────────┼─────────────────────────┐         │
│          │                         │                         │         │
│  ┌───────▼────────┐    ┌──────────▼──────────┐   ┌─────────▼───────┐ │
│  │  RULE: IAM     │    │  RULE: GuardDuty     │   │  RULE: Config   │ │
│  │  Changes       │    │  High Severity       │   │  Non-Compliant  │ │
│  └───────┬────────┘    └──────────┬──────────┘   └─────────┬───────┘ │
│          │                        │                         │         │
│  ┌───────▼────────┐    ┌──────────▼──────────┐   ┌─────────▼───────┐ │
│  │ Lambda:        │    │ Lambda:              │   │ Lambda:         │ │
│  │ IAMPolicyAudit │    │ ThreatResponse       │   │ ComplianceFix   │ │
│  │                │    │                      │   │                 │ │
│  │ • Analyze      │    │ • Isolate EC2        │   │ • Enable MFA    │ │
│  │ • Alert        │    │ • Revoke credentials │   │ • Fix S3 ACLs   │ │
│  │ • Remediate    │    │ • Block IP in WAF    │   │ • Rotate keys   │ │
│  └───────┬────────┘    └──────────┬──────────┘   └─────────┬───────┘ │
│          │                        │                         │         │
│          └─────────────────────────┴─────────────────────────┘         │
│                                    │                                    │
│                    ┌───────────────▼───────────────┐                   │
│                    │         STEP FUNCTIONS         │                   │
│                    │    (Complex Orchestration)     │                   │
│                    │                                │                   │
│                    │  Approval → Remediate → Verify │                   │
│                    └───────────────┬───────────────┘                   │
│                                    │                                    │
│          ┌─────────────────────────┼─────────────────────────┐         │
│          │                         │                         │         │
│  ┌───────▼────────┐    ┌──────────▼──────────┐   ┌─────────▼───────┐ │
│  │  SNS: Security │    │  DynamoDB:           │   │  S3:            │ │
│  │  Alerts        │    │  Audit Log           │   │  Evidence Store │ │
│  │  (PagerDuty/   │    │  (Findings +         │   │  (CloudTrail +  │ │
│  │   Slack/Email) │    │   Remediations)      │   │   Config snaps) │ │
│  └────────────────┘    └─────────────────────┘   └─────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Step Functions for Complex Security Workflows

```python
# STEP FUNCTIONS — Incident Response Orchestration
import boto3
import json

def create_incident_response_state_machine() -> str:
    """
    Creates a Step Functions state machine for security incident response.
    Orchestrates: Detect → Triage → Contain → Notify → Remediate → Verify
    """
    
    sfn = boto3.client('stepfunctions')
    
    # State machine definition
    definition = {
        "Comment": "Security Incident Response Workflow",
        "StartAt": "TriageIncident",
        "States": {
            "TriageIncident": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:TriageIncident",
                "ResultPath": "$.triage",
                "Next": "CheckSeverity",
                "Retry": [{
                    "ErrorEquals": ["Lambda.ServiceException", "Lambda.TooManyRequestsException"],
                    "IntervalSeconds": 2,
                    "MaxAttempts": 3,
                    "BackoffRate": 2
                }],
                "Catch": [{
                    "ErrorEquals": ["States.ALL"],
                    "Next": "NotifyOnError",
                    "ResultPath": "$.error"
                }]
            },
            
            "CheckSeverity": {
                "Type": "Choice",
                "Choices": [
                    {
                        "Variable": "$.triage.severity",
                        "StringEquals": "CRITICAL",
                        "Next": "ImmediateContainment"
                    },
                    {
                        "Variable": "$.triage.severity",
                        "StringEquals": "HIGH",
                        "Next": "RequestApproval"
                    }
                ],
                "Default": "LogAndMonitor"
            },
            
            "ImmediateContainment": {
                "Type": "Parallel",
                "Branches": [
                    {
                        "StartAt": "IsolateResource",
                        "States": {
                            "IsolateResource": {
                                "Type": "Task",
                                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:IsolateResource",
                                "End": True
                            }
                        }
                    },
                    {
                        "StartAt": "RevokeCredentials",
                        "States": {
                            "RevokeCredentials": {
                                "Type": "Task",
                                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:RevokeCredentials",
                                "End": True
                            }
                        }
                    },
                    {
                        "StartAt": "NotifySecurityTeam",
                        "States": {
                            "NotifySecurityTeam": {
                                "Type": "Task",
                                "Resource": "arn:aws:states:::sns:publish",
                                "Parameters": {
                                    "TopicArn": "arn:aws:sns:us-east-1:ACCOUNT:security-critical",
                                    "Message.$": "States.Format('CRITICAL INCIDENT: {}', $.triage.description)"
                                },
                                "End": True
                            }
                        }
                    }
                ],
                "Next": "VerifyContainment"
            },
            
            "RequestApproval": {
                "Type": "Task",
                "Resource": "arn:aws:states:::sqs:sendMessage.waitForTaskToken",
                "Parameters": {
                    "QueueUrl": "https://sqs.us-east-1.amazonaws.com/ACCOUNT/approval-queue",
                    "MessageBody": {
                        "TaskToken.$": "$$.Task.Token",
                        "Incident.$": "$"
                    }
                },
                "TimeoutSeconds": 3600,  # 1 hour to approve
                "Next": "ApprovedRemediation",
                "Catch": [{
                    "ErrorEquals": ["States.Timeout"],
                    "Next": "EscalateToManager"
                }]
            },
            
            "ApprovedRemediation": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:RemediateIncident",
                "Next": "VerifyContainment"
            },
            
            "VerifyContainment": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:VerifyContainment",
                "ResultPath": "$.verification",
                "Next": "ContainmentSuccessful"
            },
            
            "ContainmentSuccessful": {
                "Type": "Choice",
                "Choices": [{
                    "Variable": "$.verification.success",
                    "BooleanEquals": True,
                    "Next": "CreateIncidentReport"
                }],
                "Default": "EscalateToManager"
            },
            
            "CreateIncidentReport": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:CreateIncidentReport",
                "Next": "IncidentResolved"
            },
            
            "LogAndMonitor": {
                "Type": "Task",
                "Resource": "arn:aws:lambda:us-east-1:ACCOUNT:function:LogFinding",
                "End": True
            },
            
            "EscalateToManager": {
                "Type": "Task",
                "Resource": "arn:aws:states:::sns:publish",
                "Parameters": {
                    "TopicArn": "arn:aws:sns:us-east-1:ACCOUNT:security-escalation",
                    "Message.$": "States.Format('ESCALATION REQUIRED: {}', $.triage.description)"
                },
                "End": True
            },
            
            "NotifyOnError": {
                "Type": "Task",
                "Resource": "arn:aws:states:::sns:publish",
                "Parameters": {
                    "TopicArn": "arn:aws:sns:us-east-1:ACCOUNT:security-errors",
                    "Message.$": "$.error.Cause"
                },
                "End": True
            },
            
            "IncidentResolved": {
                "Type": "Succeed"
            }
        }
    }
    
    response = sfn.create_state_machine(
        name="SecurityIncidentResponse",
        definition=json.dumps(definition),
        roleArn="arn:aws:iam::ACCOUNT:role/StepFunctionsSecurityRole",
        type="STANDARD",
        loggingConfiguration={
            "level": "ALL",
            "includeExecutionData": True,
            "destinations": [{
                "cloudWatchLogsLogGroup": {
                    "logGroupArn": "arn:aws:logs:us-east-1:ACCOUNT:log-group:/aws/states/security-incidents:*"
                }
            }]
        }
    )
    
    return response['stateMachineArn']
```

---

## 5. Security Automation Patterns {#security-automation}

### 5.1 Complete Security Posture Assessment Tool

```python
#!/usr/bin/env python3
"""
AWS Security Posture Assessment Tool
Checks CIS AWS Foundations Benchmark controls
"""

import boto3
import json
import logging
from dataclasses import dataclass, field, asdict
from typing import List, Optional
from datetime import datetime, timezone, timedelta
from concurrent.futures import ThreadPoolExecutor, as_completed

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)


@dataclass
class SecurityFinding:
    check_id: str
    title: str
    severity: str  # CRITICAL, HIGH, MEDIUM, LOW
    status: str    # PASS, FAIL, WARNING
    resource: str
    description: str
    remediation: str
    account_id: str = ""
    region: str = ""
    timestamp: str = field(default_factory=lambda: datetime.now(timezone.utc).isoformat())


class AWSSecurityAssessor:
    """
    Comprehensive AWS security assessment tool.
    Implements CIS AWS Foundations Benchmark v1.4 checks.
    """
    
    def __init__(self, regions: List[str] = None):
        self.session = boto3.Session()
        self.account_id = self.session.client('sts').get_caller_identity()['Account']
        self.regions = regions or ['us-east-1', 'us-west-2', 'ap-south-1']
        self.findings: List[SecurityFinding] = []
    
    def run_full_assessment(self) -> dict:
        """Run all security checks and return comprehensive report"""
        logger.info(f"Starting security assessment for account {self.account_id}")
        
        # Run checks in parallel for speed
        check_functions = [
            self.check_root_account_mfa,
            self.check_iam_password_policy,
            self.check_access_key_rotation,
            self.check_cloudtrail_enabled,
            self.check_s3_public_access,
            self.check_security_groups,
            self.check_vpc_flow_logs,
            self.check_guardduty_enabled,
            self.check_config_enabled,
            self.check_unused_credentials
        ]
        
        with ThreadPoolExecutor(max_workers=5) as executor:
            futures = {executor.submit(check): check.__name__ 
                      for check in check_functions}
            
            for future in as_completed(futures):
                check_name = futures[future]
                try:
                    results = future.result()
                    if results:
                        self.findings.extend(results if isinstance(results, list) 
                                           else [results])
                except Exception as e:
                    logger.error(f"Check {check_name} failed: {e}")
        
        return self._generate_report()
    
    def check_root_account_mfa(self) -> List[SecurityFinding]:
        """CIS 1.1 — Avoid the use of the root account"""
        findings = []
        iam = self.session.client('iam')
        
        try:
            summary = iam.get_account_summary()['SummaryMap']
            
            if summary.get('AccountMFAEnabled', 0) == 0:
                findings.append(SecurityFinding(
                    check_id="CIS-1.1",
                    title="Root Account MFA Not Enabled",
                    severity="CRITICAL",
                    status="FAIL",
                    resource=f"arn:aws:iam::{self.account_id}:root",
                    description="The root account does not have MFA enabled. "
                               "Root account has unrestricted access to all resources.",
                    remediation="Enable MFA on root account: IAM Console → "
                               "Security credentials → Assign MFA device",
                    account_id=self.account_id
                ))
            else:
                findings.append(SecurityFinding(
                    check_id="CIS-1.1",
                    title="Root Account MFA Enabled",
                    severity="CRITICAL",
                    status="PASS",
                    resource=f"arn:aws:iam::{self.account_id}:root",
                    description="Root account MFA is enabled",
                    remediation="N/A",
                    account_id=self.account_id
                ))
        
        except Exception as e:
            logger.error(f"Error checking root MFA: {e}")
        
        return findings
    
    def check_access_key_rotation(self) -> List[SecurityFinding]:
        """CIS 1.4 — Ensure access keys are rotated every 90 days"""
        findings = []
        iam = self.session.client('iam')
        threshold = datetime.now(timezone.utc) - timedelta(days=90)
        
        paginator = iam.get_paginator('list_users')
        
        for page in paginator.paginate():
            for user in page['Users']:
                key_paginator = iam.get_paginator('list_access_keys')
                
                for key_page in key_paginator.paginate(UserName=user['UserName']):
                    for key in key_page['AccessKeyMetadata']:
                        if key['Status'] == 'Active' and key['CreateDate'] < threshold:
                            age_days = (datetime.now(timezone.utc) - key['CreateDate']).days
                            
                            findings.append(SecurityFinding(
                                check_id="CIS-1.4",
                                title=f"Access Key Older Than 90 Days",
                                severity="HIGH",
                                status="FAIL",
                                resource=f"arn:aws:iam::{self.account_id}:user/{user['UserName']}",
                                description=f"User {user['UserName']} has access key "
                                           f"{key['AccessKeyId']} that is {age_days} days old",
                                remediation=f"Rotate access key for user {user['UserName']}: "
                                           f"Create new key, update applications, delete old key",
                                account_id=self.account_id
                            ))
        
        return findings
    
    def check_security_groups(self) -> List[SecurityFinding]:
        """CIS 4.1/4.2 — No unrestricted SSH/RDP access"""
        findings = []
        
        for region in self.regions:
            ec2 = self.session.client('ec2', region_name=region)
            
            try:
                paginator = ec2.get_paginator('describe_security_groups')
                
                for page in paginator.paginate():
                    for sg in page['SecurityGroups']:
                        for rule in sg.get('IpPermissions', []):
                            from_port = rule.get('FromPort', 0)
                            to_port = rule.get('ToPort', 65535)
                            
                            for ip_range in rule.get('IpRanges', []):
                                if ip_range.get('CidrIp') == '0.0.0.0/0':
                                    
                                    # Check for SSH (22) or RDP (3389)
                                    if (from_port <= 22 <= to_port or 
                                            from_port <= 3389 <= to_port or
                                            (from_port == -1)):  # All traffic
                                        
                                        port_name = "SSH" if from_port <= 22 <= to_port else "RDP"
                                        
                                        findings.append(SecurityFinding(
                                            check_id="CIS-4.1" if port_name == "SSH" else "CIS-4.2",
                                            title=f"Unrestricted {port_name} Access",
                                            severity="HIGH",
                                            status="FAIL",
                                            resource=f"arn:aws:ec2:{region}:{self.account_id}:"
                                                    f"security-group/{sg['GroupId']}",
                                            description=f"Security group {sg['GroupName']} "
                                                       f"({sg['GroupId']}) allows unrestricted "
                                                       f"{port_name} access from 0.0.0.0/0",
                                            remediation=f"Restrict {port_name} access to specific "
                                                       f"IP ranges or use AWS Systems Manager "
                                                       f"Session Manager instead",
                                            account_id=self.account_id,
                                            region=region
                                        ))
            
            except Exception as e:
                logger.error(f"Error checking security groups in {region}: {e}")
        
        return findings
    
    def check_guardduty_enabled(self) -> List[SecurityFinding]:
        """Check GuardDuty is enabled in all regions"""
        findings = []
        
        for region in self.regions:
            gd = self.session.client('guardduty', region_name=region)
            
            try:
                detectors = gd.list_detectors()['DetectorIds']
                
                if not detectors:
                    findings.append(SecurityFinding(
                        check_id="CUSTOM-GD-1",
                        title="GuardDuty Not Enabled",
                        severity="HIGH",
                        status="FAIL",
                        resource=f"arn:aws:guardduty:{region}:{self.account_id}",
                        description=f"GuardDuty is not enabled in region {region}",
                        remediation="Enable GuardDuty: aws guardduty create-detector "
                                   "--enable --finding-publishing-frequency FIFTEEN_MINUTES",
                        account_id=self.account_id,
                        region=region
                    ))
                else:
                    # Check detector status
                    detector = gd.get_detector(DetectorId=detectors[0])
                    if detector['Status'] != 'ENABLED':
                        findings.append(SecurityFinding(
                            check_id="CUSTOM-GD-1",
                            title="GuardDuty Detector Disabled",
                            severity="HIGH",
                            status="FAIL",
                            resource=f"arn:aws:guardduty:{region}:{self.account_id}:"
                                    f"detector/{detectors[0]}",
                            description=f"GuardDuty detector exists but is disabled in {region}",
                            remediation="Enable the GuardDuty detector",
                            account_id=self.account_id,
                            region=region
                        ))
            
            except Exception as e:
                logger.error(f"Error checking GuardDuty in {region}: {e}")
        
        return findings
    
    def check_cloudtrail_enabled(self) -> List[SecurityFinding]:
        """CIS 2.1 — Ensure CloudTrail is enabled in all regions"""
        findings = []
        ct = self.session.client('cloudtrail')
        
        try:
            trails = ct.describe_trails(includeShadowTrails=False)['trailList']
            
            multi_region_trails = [t for t in trails if t.get('IsMultiRegionTrail', False)]
            
            if not multi_region_trails:
                findings.append(SecurityFinding(
                    check_id="CIS-2.1",
                    title="No Multi-Region CloudTrail",
                    severity="HIGH",
                    status="FAIL",
                    resource=f"arn:aws:cloudtrail:us-east-1:{self.account_id}",
                    description="No multi-region CloudTrail trail found. "
                               "API activity in some regions may not be logged.",
                    remediation="Create a multi-region CloudTrail trail with "
                               "log file validation enabled",
                    account_id=self.account_id
                ))
            else:
                # Check log file validation
                for trail in multi_region_trails:
                    if not trail.get('LogFileValidationEnabled', False):
                        findings.append(SecurityFinding(
                            check_id="CIS-2.2",
                            title="CloudTrail Log File Validation Disabled",
                            severity="MEDIUM",
                            status="FAIL",
                            resource=trail['TrailARN'],
                            description=f"Trail {trail['Name']} does not have "
                                       f"log file validation enabled",
                            remediation="Enable log file validation: "
                                       "aws cloudtrail update-trail --name TRAIL_NAME "
                                       "--enable-log-file-validation",
                            account_id=self.account_id
                        ))
        
        except Exception as e:
            logger.error(f"Error checking CloudTrail: {e}")
        
        return findings
    
    def check_s3_public_access(self) -> List[SecurityFinding]:
        """Check S3 buckets for public access"""
        findings = []
        s3 = self.session.client('s3')
        
        try:
            buckets = s3.list_buckets()['Buckets']
            
            for bucket in buckets:
                bucket_name = bucket['Name']
                
                try:
                    # Check public access block
                    pab = s3.get_public_access_block(Bucket=bucket_name)
                    config = pab['PublicAccessBlockConfiguration']
                    
                    if not all([
                        config.get('BlockPublicAcls', False),
                        config.get('BlockPublicPolicy', False),
                        config.get('IgnorePublicAcls', False),
                        config.get('RestrictPublicBuckets', False)
                    ]):
                        findings.append(SecurityFinding(
                            check_id="CUSTOM-S3-1",
                            title="S3 Bucket Public Access Not Fully Blocked",
                            severity="HIGH",
                            status="FAIL",
                            resource=f"arn:aws:s3:::{bucket_name}",
                            description=f"Bucket {bucket_name} does not have all "
                                       f"public access block settings enabled",
                            remediation=f"Enable all public access block settings: "
                                       f"aws s3api put-public-access-block "
                                       f"--bucket {bucket_name} "
                                       f"--public-access-block-configuration "
                                       f"BlockPublicAcls=true,IgnorePublicAcls=true,"
                                       f"BlockPublicPolicy=true,RestrictPublicBuckets=true",
                            account_id=self.account_id
                        ))
                
                except s3.exceptions.NoSuchPublicAccessBlockConfiguration:
                    findings.append(SecurityFinding(
                        check_id="CUSTOM-S3-1",
                        title="S3 Bucket Has No Public Access Block Configuration",
                        severity="HIGH",
                        status="FAIL",
                        resource=f"arn:aws:s3:::{bucket_name}",
                        description=f"Bucket {bucket_name} has no public access "
                                   f"block configuration",
                        remediation="Configure public access block settings",
                        account_id=self.account_id
                    ))
        
        except Exception as e:
            logger.error(f"Error checking S3 public access: {e}")
        
        return findings
    
    def check_iam_password_policy(self) -> List[SecurityFinding]:
        """CIS 1.5-1.11 — IAM password policy checks"""
        findings = []
        iam = self.session.client('iam')
        
        try:
            policy = iam.get_account_password_policy()['PasswordPolicy']
            
            checks = [
                ('MinimumPasswordLength', 14, 'CIS-1.9', 
                 'Password minimum length should be 14+'),
                ('RequireUppercaseCharacters', True, 'CIS-1.5',
                 'Password should require uppercase characters'),
                ('RequireLowercaseCharacters', True, 'CIS-1.6',
                 'Password should require lowercase characters'),
                ('RequireNumbers', True, 'CIS-1.7',
                 'Password should require numbers'),
                ('RequireSymbols', True, 'CIS-1.8',
                 'Password should require symbols'),
                ('MaxPasswordAge', 90, 'CIS-1.11',
                 'Password maximum age should be 90 days or less'),
                ('PasswordReusePrevention', 24, 'CIS-1.10',
                 'Password reuse prevention should be 24 or more'),
            ]
            
            for check_key, required_value, check_id, description in checks:
                actual_value = policy.get(check_key)
                
                failed = False
                if isinstance(required_value, bool):
                    failed = actual_value != required_value
                elif check_key == 'MinimumPasswordLength':
                    failed = (actual_value or 0) < required_value
                elif check_key == 'MaxPasswordAge':
                    failed = actual_value is None or actual_value > required_value
                elif check_key == 'PasswordReusePrevention':
                    failed = (actual_value or 0) < required_value
                
                if failed:
                    findings.append(SecurityFinding(
                        check_id=check_id,
                        title=f"Password Policy: {description}",
                        severity="MEDIUM",
                        status="FAIL",
                        resource=f"arn:aws:iam::{self.account_id}:account",
                        description=f"Current value: {actual_value}, Required: {required_value}",
                        remediation=f"Update password policy: aws iam update-account-password-policy",
                        account_id=self.account_id
                    ))
        
        except iam.exceptions.NoSuchEntityException:
            findings.append(SecurityFinding(
                check_id="CIS-1.5",
                title="No IAM Password Policy",
                severity="HIGH",
                status="FAIL",
                resource=f"arn:aws:iam::{self.account_id}:account",
                description="No IAM password policy is configured",
                remediation="Create a strong password policy",
                account_id=self.account_id
            ))
        
        return findings
    
    def check_vpc_flow_logs(self) -> List[SecurityFinding]:
        """Check VPC Flow Logs are enabled"""
        findings = []
        
        for region in self.regions:
            ec2 = self.session.client('ec2', region_name=region)
            
            try:
                vpcs = ec2.describe_vpcs()['Vpcs']
                flow_logs = ec2.describe_flow_logs()['FlowLogs']
                
                vpc_ids_with_logs = {fl['ResourceId'] for fl in flow_logs 
                                    if fl['FlowLogStatus'] == 'ACTIVE'}
                
                for vpc in vpcs:
                    vpc_id = vpc['VpcId']
                    if vpc_id not in vpc_ids_with_logs:
                        findings.append(SecurityFinding(
                            check_id="CIS-2.9",
                            title="VPC Flow Logs Not Enabled",
                            severity="MEDIUM",
                            status="FAIL",
                            resource=f"arn:aws:ec2:{region}:{self.account_id}:vpc/{vpc_id}",
                            description=f"VPC {vpc_id} in {region} does not have flow logs enabled",
                            remediation=f"Enable VPC flow logs: aws ec2 create-flow-logs "
                                       f"--resource-type VPC --resource-ids {vpc_id} "
                                       f"--traffic-type ALL --log-destination-type cloud-watch-logs",
                            account_id=self.account_id,
                            region=region
                        ))
            
            except Exception as e:
                logger.error(f"Error checking VPC flow logs in {region}: {e}")
        
        return findings
    
    def check_unused_credentials(self) -> List[SecurityFinding]:
        """CIS 1.3 — Disable credentials unused for 90+ days"""
        findings = []
        iam = self.session.client('iam')
        threshold = datetime.now(timezone.utc) - timedelta(days=90)
        
        try:
            # Get credential report
            iam.generate_credential_report()
            import time
            time.sleep(2)  # Wait for report generation
            
            report = iam.get_credential_report()
            import csv
            import io
            
            reader = csv.DictReader(io.StringIO(report['Content'].decode('utf-8')))
            
            for row in reader:
                if row['user'] == '<root_account>':
                    continue
                
                # Check password last used
                password_last_used = row.get('password_last_used', 'N/A')
                if password_last_used not in ['N/A', 'no_information', 'not_supported']:
                    try:
                        last_used = datetime.fromisoformat(
                            password_last_used.replace('Z', '+00:00')
                        )
                        if last_used < threshold:
                            findings.append(SecurityFinding(
                                check_id="CIS-1.3",
                                title="Unused IAM User Credentials",
                                severity="MEDIUM",
                                status="FAIL",
                                resource=f"arn:aws:iam::{self.account_id}:user/{row['user']}",
                                description=f"User {row['user']} password not used since "
                                           f"{password_last_used}",
                                remediation=f"Disable or delete user {row['user']} if no longer needed",
                                account_id=self.account_id
                            ))
                    except ValueError:
                        pass
        
        except Exception as e:
            logger.error(f"Error checking unused credentials: {e}")
        
        return findings
    
    def _generate_report(self) -> dict:
        """Generate comprehensive security report"""
        severity_counts = {'CRITICAL': 0, 'HIGH': 0, 'MEDIUM': 0, 'LOW': 0}
        status_counts = {'PASS': 0, 'FAIL': 0, 'WARNING': 0}
        
        for finding in self.findings:
            if finding.status == 'FAIL':
                severity_counts[finding.severity] = \
                    severity_counts.get(finding.severity, 0) + 1
            status_counts[finding.status] = status_counts.get(finding.status, 0) + 1
        
        # Calculate security score
        total_checks = len(self.findings)
        passed_checks = status_counts['PASS']
        score = (passed_checks / total_checks * 100) if total_checks > 0 else 0
        
        return {
            'account_id': self.account_id,
            'assessment_date': datetime.now(timezone.utc).isoformat(),
            'security_score': round(score, 1),
            'summary': {
                'total_checks': total_checks,
                'status_counts': status_counts,
                'severity_counts': severity_counts
            },
            'findings': [asdict(f) for f in self.findings],
            'critical_findings': [
                asdict(f) for f in self.findings 
                if f.severity == 'CRITICAL' and f.status == 'FAIL'
            ]
        }


# MAIN EXECUTION
if __name__ == "__main__":
    assessor = AWSSecurityAssessor(regions=['us-east-1', 'us-west-2'])
    report = assessor.run_full_assessment()
    
    print(f"\n{'='*60}")
    print(f"SECURITY ASSESSMENT REPORT")
    print(f"Account: {report['account_id']}")
    print(f"Security Score: {report['security_score']}%")
    print(f"{'='*60}")
    print(f"Total Checks: {report['summary']['total_checks']}")
    print(f"Passed: {report['summary']['status_counts']['PASS']}")
    print(f"Failed: {report['summary']['status_counts']['FAIL']}")
    print(f"\nSeverity Breakdown:")
    for sev, count in report['summary']['severity_counts'].items():
        if count > 0:
            print(f"  {sev}: {count}")
    
    if report['critical_findings']:
        print(f"\n⚠️  CRITICAL FINDINGS ({len(report['critical_findings'])}):")
        for f in report['critical_findings']:
            print(f"  • {f['title']}: {f['description']}")
    
    # Save full report
    with open(f"security_report_{report['account_id']}.json", 'w') as f:
        json.dump(report, f, indent=2, default=str)
    
    print(f"\nFull report saved to security_report_{report['account_id']}.json")
```

---

## 6. Interview Q&A Bank {#qa-bank}

### 6.1 IAM Questions

**Q1: Explain the difference between IAM roles and IAM users. When do you use each?**

> **A:** IAM Users are long-term identities with permanent credentials (password + access keys) — use for human users who need console/CLI access. IAM Roles are temporary identities with short-lived credentials via STS — use for EC2 instances, Lambda functions, cross-account access, and federated users. **Best practice: Never create access keys for applications — always use roles.** In my multi-account setup at ValueMomentum, I eliminated all application-level access keys by implementing IRSA (IAM Roles for Service Accounts) for EKS workloads and instance profiles for EC2.

**Q2: What is the difference between a Permission Boundary and an SCP?**

> **A:** Both limit maximum permissions but at different scopes. **Permission Boundaries** are IAM policies attached to a specific user or role — they define the maximum permissions that identity can have, regardless of what identity-based policies grant. They're used to delegate IAM administration safely (e.g., let developers create roles but only within a boundary). **SCPs** operate at the AWS Organizations level — they apply to entire OUs or accounts and restrict what any principal in that account can do. SCPs don't grant permissions; they only restrict. Key difference: Permission Boundaries affect a single identity; SCPs affect an entire account or OU.

**Q3: How does cross-account access work in AWS?**

> **A:** Cross-account access requires trust from BOTH sides. The **trusting account** (where the resource lives) has a role with a trust policy that allows the **trusted account** (where the principal lives) to assume it. The principal in the trusted account needs an identity policy that allows `sts:AssumeRole` on that role ARN. For extra security, use an **External ID** condition in the trust policy to prevent the confused deputy problem. In my work, I automated this with boto3's `sts.assume_role()` and used context managers to ensure temporary credentials are properly scoped and cleaned up.

**Q4: What is the confused deputy problem in AWS?**

> **A:** The confused deputy problem occurs when a trusted service (like a third-party SaaS) is tricked into using its elevated permissions on behalf of an attacker. Example: A third-party monitoring tool has a role in your account. An attacker creates their own AWS account and tricks the tool into assuming your role using the tool's ARN. **Solution:** Use an External ID in the trust policy — a secret shared only between you and the legitimate third party. The attacker can't know this External ID, so they can't exploit the trust relationship.

---

### 6.2 Lambda & EventBridge Questions

**Q5: How do you handle Lambda cold starts in production?**

> **A:** Cold starts happen when Lambda needs to initialize a new execution environment. Mitigation strategies:
> 1. **Provisioned Concurrency** — pre-warms N instances, eliminates cold starts for those
> 2. **Move initialization outside handler** — SDK clients, DB connections initialized once per container
> 3. **Minimize package size** — use Lambda Layers for shared dependencies, avoid unnecessary imports
> 4. **Use ARM64 (Graviton2)** — generally faster init, 20% cheaper
> 5. **Keep functions warm** — EventBridge scheduled rule every 5 minutes for critical functions
> 6. **Lambda SnapStart** (Java) — snapshots after init phase
>
> In security automation, cold starts are acceptable since we're not in the critical path. For customer-facing APIs, I'd use Provisioned Concurrency.

**Q6: EventBridge vs SNS vs SQS — when do you use each?**

> **A:**
> - **EventBridge**: Event routing with pattern matching. Use when you need to filter events by content, route to multiple targets, or integrate with AWS services. Best for event-driven architectures where the event source is AWS services or custom apps.
> - **SNS**: Fan-out pub/sub. Use when you need to push the same message to multiple subscribers simultaneously (Lambda + SQS + Email + HTTP). No filtering by content (unless using message filtering).
> - **SQS**: Decoupled queue for reliable message delivery. Use when you need guaranteed delivery, rate limiting, or want consumers to pull at their own pace. Handles backpressure naturally.
>
> **Typical pattern**: EventBridge → SNS (fan-out) → SQS (per-consumer queue) → Lambda (processor)

**Q7: How do you handle Lambda failures and ensure no events are lost?**

> **A:** Multi-layer approach:
> 1. **Dead Letter Queue (DLQ)** — failed events go to SQS/SNS DLQ for manual review
> 2. **EventBridge retry policy** — configure MaximumRetryAttempts (0-185) and MaximumEventAgeInSeconds
> 3. **Lambda Destinations** — on-failure destination to SQS/SNS/Lambda/EventBridge
> 4. **Idempotency** — design handlers to be idempotent using DynamoDB conditional writes with event ID
> 5. **Structured error handling** — catch specific exceptions, log with context, don't swallow errors
> 6. **CloudWatch Alarms** — alert on Lambda errors, DLQ message count

---

### 6.3 Python/Boto3 Questions

**Q8: How do you handle AWS API throttling in boto3?**

> **A:** AWS throttles API calls when you exceed service limits. Handling strategies:
> 1. **Built-in retry config** — boto3 has automatic retry with exponential backoff by default (3 retries). Customize with `Config(retries={'max_attempts': 10, 'mode': 'adaptive'})`
> 2. **Custom retry decorator** — catch `ThrottlingException` and `RequestLimitExceeded`, implement exponential backoff with jitter
> 3. **Pagination** — use paginators instead of manual loops to avoid hitting list API limits
> 4. **Caching** — cache frequently-read data (account info, org structure) with `@lru_cache`
> 5. **Adaptive mode** — boto3's adaptive retry mode automatically adjusts retry rate based on throttling signals

```python
from botocore.config import Config

# Configure adaptive retry
config = Config(
    retries={
        'max_attempts': 10,
        'mode': 'adaptive'  # Automatically adjusts based on throttling
    },
    max_pool_connections=50  # For high-concurrency scenarios
)

client = boto3.client('iam', config=config)
```

**Q9: How do you optimize boto3 for performance in large-scale automation?**

> **A:**
> 1. **Reuse clients** — initialize outside Lambda handler, reuse across warm invocations
> 2. **Connection pooling** — `Config(max_pool_connections=50)` for concurrent requests
> 3. **Parallel execution** — `ThreadPoolExecutor` for I/O-bound operations, `ProcessPoolExecutor` for CPU-bound
> 4. **Pagination** — always use paginators, never assume single-page responses
> 5. **Selective field retrieval** — use `ProjectionExpression` (DynamoDB), `select` parameters where available
> 6. **Regional clients** — create clients in the same region as your Lambda to minimize latency
> 7. **Async boto3** — use `aioboto3` for truly async operations in high-throughput scenarios

**Q10: Write a function to find all S3 buckets with public access and remediate them.**

```python
import boto3
import json
import logging
from typing import List, Tuple

logger = logging.getLogger(__name__)

def find_and_remediate_public_buckets(
    dry_run: bool = True,
    notify_sns_arn: str = None
) -> dict:
    """
    Finds all S3 buckets with public access and optionally remediates.
    
    Args:
        dry_run: If True, only report — don't remediate
        notify_sns_arn: SNS topic ARN for notifications
    
    Returns:
        Report of findings and actions taken
    """
    s3 = boto3.client('s3')
    sns = boto3.client('sns') if notify_sns_arn else None
    
    public_buckets = []
    remediated_buckets = []
    failed_remediations = []
    
    # List all buckets
    buckets = s3.list_buckets()['Buckets']
    logger.info(f"Scanning {len(buckets)} S3 buckets")
    
    for bucket in buckets:
        bucket_name = bucket['Name']
        
        is_public, reason = check_bucket_public_access(s3, bucket_name)
        
        if is_public:
            public_buckets.append({
                'bucket': bucket_name,
                'reason': reason
            })
            logger.warning(f"Public bucket found: {bucket_name} — {reason}")
            
            if not dry_run:
                success, error = remediate_bucket(s3, bucket_name)
                if success:
                    remediated_buckets.append(bucket_name)
                    logger.info(f"Remediated: {bucket_name}")
                else:
                    failed_remediations.append({'bucket': bucket_name, 'error': error})
                    logger.error(f"Failed to remediate {bucket_name}: {error}")
    
    report = {
        'total_buckets_scanned': len(buckets),
        'public_buckets_found': len(public_buckets),
        'public_buckets': public_buckets,
        'dry_run': dry_run,
        'remediated': remediated_buckets if not dry_run else [],
        'failed_remediations': failed_remediations
    }
    
    # Send notification
    if notify_sns_arn and public_buckets:
        message = format_s3_report(report)
        sns.publish(
            TopicArn=notify_sns_arn,
            Subject=f"S3 Public Access Report — {len(public_buckets)} buckets found",
            Message=message
        )
    
    return report


def check_bucket_public_access(s3_client, bucket_name: str) -> Tuple[bool, str]:
    """
    Comprehensive check for bucket public access.
    Checks: Public Access Block, ACL, Bucket Policy
    """
    
    # Check 1: Public Access Block settings
    try:
        pab = s3_client.get_public_access_block(Bucket=bucket_name)
        config = pab['PublicAccessBlockConfiguration']
        
        if not all([
            config.get('BlockPublicAcls', False),
            config.get('BlockPublicPolicy', False),
            config.get('IgnorePublicAcls', False),
            config.get('RestrictPublicBuckets', False)
        ]):
            return True, f"Public access block not fully enabled: {config}"
    
    except s3_client.exceptions.NoSuchPublicAccessBlockConfiguration:
        return True, "No public access block configuration"
    except Exception as e:
        logger.error(f"Error checking PAB for {bucket_name}: {e}")
    
    # Check 2: Bucket ACL
    try:
        acl = s3_client.get_bucket_acl(Bucket=bucket_name)
        for grant in acl.get('Grants', []):
            grantee = grant.get('Grantee', {})
            if grantee.get('URI') in [
                'http://acs.amazonaws.com/groups/global/AllUsers',
                'http://acs.amazonaws.com/groups/global/AuthenticatedUsers'
            ]:
                return True, f"Public ACL grant: {grant['Permission']} to {grantee['URI']}"
    except Exception as e:
        logger.error(f"Error checking ACL for {bucket_name}: {e}")
    
    # Check 3: Bucket Policy
    try:
        policy = json.loads(
            s3_client.get_bucket_policy(Bucket=bucket_name)['Policy']
        )
        for stmt in policy.get('Statement', []):
            if (stmt.get('Effect') == 'Allow' and 
                    stmt.get('Principal') in ['*', {'AWS': '*'}]):
                return True, f"Bucket policy allows public access: {stmt.get('Sid', 'unnamed')}"
    except s3_client.exceptions.NoSuchBucketPolicy:
        pass  # No policy is fine
    except Exception as e:
        logger.error(f"Error checking policy for {bucket_name}: {e}")
    
    return False, "No public access detected"


def remediate_bucket(s3_client, bucket_name: str) -> Tuple[bool, str]:
    """Apply public access block to remediate public bucket"""
    try:
        s3_client.put_public_access_block(
            Bucket=bucket_name,
            PublicAccessBlockConfiguration={
                'BlockPublicAcls': True,
                'IgnorePublicAcls': True,
                'BlockPublicPolicy': True,
                'RestrictPublicBuckets': True
            }
        )
        
        # Add bucket tag to track remediation
        s3_client.put_bucket_tagging(
            Bucket=bucket_name,
            Tagging={
                'TagSet': [
                    {'Key': 'SecurityRemediation', 'Value': 'PublicAccessBlocked'},
                    {'Key': 'RemediationDate', 'Value': 
                     __import__('datetime').datetime.utcnow().isoformat()}
                ]
            }
        )
        
        return True, None
    
    except Exception as e:
        return False, str(e)


def format_s3_report(report: dict) -> str:
    return f"""
S3 PUBLIC ACCESS SECURITY REPORT
{'='*50}
Buckets Scanned: {report['total_buckets_scanned']}
Public Buckets Found: {report['public_buckets_found']}
Mode: {'DRY RUN' if report['dry_run'] else 'REMEDIATION'}

PUBLIC BUCKETS:
{chr(10).join(f"  • {b['bucket']}: {b['reason']}" for b in report['public_buckets'])}

{f"REMEDIATED ({len(report['remediated'])}):" if not report['dry_run'] else ""}
{chr(10).join(f"  ✓ {b}" for b in report.get('remediated', []))}

{f"FAILED ({len(report['failed_remediations'])}):" if report.get('failed_remediations') else ""}
{chr(10).join(f"  ✗ {f['bucket']}: {f['error']}" for f in report.get('failed_remediations', []))}
"""
```

---

### 6.4 Architecture & Design Questions

**Q11: Design a serverless solution to automatically detect and respond to IAM credential compromise.**

```
┌─────────────────────────────────────────────────────────────────────┐
│         CREDENTIAL COMPROMISE DETECTION & RESPONSE                  │
│                                                                     │
│  DETECTION LAYER:                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐  │
│  │  CloudTrail  │  │  GuardDuty   │  │  CloudWatch Anomaly      │  │
│  │  (API calls) │  │  (ML-based   │  │  Detection               │  │
│  │              │  │   detection) │  │  (unusual API patterns)  │  │
│  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────────┘  │
│         └─────────────────┴──────────────────────┘                  │
│                                    │                                 │
│                    ┌───────────────▼──────────────┐                 │
│                    │      EVENTBRIDGE BUS          │                 │
│                    │  Rules:                       │                 │
│                    │  • Unusual geo-location       │                 │
│                    │  • API calls from Tor/VPN     │                 │
│                    │  • Impossible travel          │                 │
│                    │  • High-volume API calls      │                 │
│                    └───────────────┬──────────────┘                 │
│                                    │                                 │
│                    ┌───────────────▼──────────────┐                 │
│                    │   STEP FUNCTIONS WORKFLOW     │                 │
│                    │                               │                 │
│                    │  1. Enrich (IP reputation,    │                 │
│                    │     user context)             │                 │
│                    │  2. Score risk (0-100)        │                 │
│                    │  3. Decision:                 │                 │
│                    │     Score > 80: Auto-revoke   │                 │
│                    │     Score 50-80: Alert+Approve│                 │
│                    │     Score < 50: Log only      │                 │
│                    │  4. Contain (if approved)     │                 │
│                    │  5. Notify                    │                 │
│                    │  6. Create IR ticket          │                 │
│                    └───────────────┬──────────────┘                 │
│                                    │                                 │
│  RESPONSE ACTIONS:                 │                                 │
│  ┌─────────────┐  ┌────────────────▼──┐  ┌──────────────────────┐  │
│  │ Deactivate  │  │  Add Deny policy  │  │  Notify via          │  │
│  │ Access Keys │  │  to user/role     │  │  PagerDuty/Slack/    │  │
│  │             │  │  (immediate block)│  │  Email               │  │
│  └─────────────┘  └───────────────────┘  └──────────────────────┘  │
│                                                                     │
│  AUDIT TRAIL:                                                       │
│  All actions → DynamoDB (audit log) + S3 (evidence) + SecurityHub  │
└─────────────────────────────────────────────────────────────────────┘
```

**Q12: How would you implement least-privilege access at scale across 50+ AWS accounts?**

> **A:** This is exactly what I built at ValueMomentum. The approach:
>
> 1. **Identity Center Permission Sets** — define role templates centrally (ReadOnly, Developer, SecurityAudit, etc.) with inline policies scoped to specific services
> 2. **SCPs as guardrails** — apply at OU level to enforce maximum permissions regardless of what permission sets grant
> 3. **Permission Boundaries** — attach to all developer-created roles to prevent privilege escalation
> 4. **Automated access reviews** — Lambda + EventBridge scheduled job runs weekly, generates access reports, flags unused permissions
> 5. **IAM Access Analyzer** — enabled in all accounts, findings fed to Security Hub, auto-remediated via Lambda
> 6. **Terraform modules** — all IAM resources created via IaC with mandatory least-privilege review in PR process
> 7. **Just-in-time access** — for production, use temporary elevated access via Identity Center with time-bound permission sets

---

### 6.5 Security-Specific Questions

**Q13: What is the AWS Shared Responsibility Model and how does it affect your security design?**

> **A:** AWS is responsible for security **OF** the cloud (physical infrastructure, hypervisor, managed service internals). You're responsible for security **IN** the cloud (data, IAM, network config, OS patching, application security).
>
> **Practical impact on my designs:**
> - **EC2**: I'm responsible for OS patching (use SSM Patch Manager), security groups, IAM instance profiles, data encryption
> - **RDS**: AWS manages the DB engine patching, I manage network access, encryption at rest/transit, IAM auth
> - **Lambda**: AWS manages the runtime, I manage code security, IAM execution role, VPC config, environment variable encryption
> - **S3**: AWS manages durability, I manage access policies, encryption, versioning, replication
>
> This model drives my shift-left approach — security controls at every layer I own.

**Q14: How do you handle secrets management in AWS?**

> **A:** Never hardcode secrets. Tiered approach:
> 1. **AWS Secrets Manager** — for database credentials, API keys, OAuth tokens. Supports automatic rotation (Lambda-based), cross-account access, fine-grained IAM policies
> 2. **AWS SSM Parameter Store** — for configuration values, non-sensitive parameters. SecureString type for sensitive values (KMS-encrypted). Cheaper than Secrets Manager for high-volume reads
> 3. **IAM Roles** — for AWS service-to-service auth. Never use access keys for applications
> 4. **External Secrets Operator** — in Kubernetes, syncs Secrets Manager/SSM to K8s secrets
>
> **Rotation pattern**: Secrets Manager → rotation Lambda → update DB password → update secret → applications pick up new secret on next read (no restart needed if using SDK with caching)

**Q15: Explain how you would detect and respond to a GuardDuty finding of type `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration`.**

> **A:** This finding means EC2 instance credentials (from IMDS) are being used from outside AWS — a strong indicator of credential theft.
>
> **Immediate response (automated):**
> 1. EventBridge rule triggers on this specific GuardDuty finding type
> 2. Lambda extracts: instance ID, role ARN, source IP
> 3. **Contain**: Add explicit Deny policy to the role for all actions except from AWS IP ranges
> 4. **Isolate**: Move EC2 instance to quarantine security group (no inbound/outbound except forensics)
> 5. **Preserve evidence**: Create EBS snapshot, capture memory dump via SSM
> 6. **Alert**: PagerDuty P1 incident, Slack #security-incidents
>
> **Investigation:**
> - CloudTrail: What API calls were made with the stolen credentials?
> - VPC Flow Logs: What data was exfiltrated?
> - GuardDuty: Any related findings?
>
> **Root cause**: How did the attacker get code execution on the EC2? SSRF? RCE? Compromised deployment?
>
> **Prevention**: Enforce IMDSv2 (hop limit=1 blocks SSRF), use IRSA for EKS instead of node instance profiles

---

## 7. System Design Scenarios {#system-design}

### 7.1 Design: Multi-Account Security Monitoring Platform

```
┌─────────────────────────────────────────────────────────────────────────┐
│         ENTERPRISE MULTI-ACCOUNT SECURITY MONITORING                   │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    MEMBER ACCOUNTS (N accounts)                  │   │
│  │                                                                  │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │   │
│  │  │CloudTrail│  │GuardDuty │  │  Config  │  │Security Hub  │   │   │
│  │  │ (enabled)│  │(delegated│  │(delegated│  │(delegated    │   │   │
│  │  │          │  │ admin)   │  │ admin)   │  │ admin)       │   │   │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └──────┬───────┘   │   │
│  │       │             │             │               │            │   │
│  │       └─────────────┴─────────────┴───────────────┘            │   │
│  │                              │                                  │   │
│  │                    ┌─────────▼──────────┐                      │   │
│  │                    │  S3 (Log Archive)  │                      │   │
│  │                    │  (centralized in   │                      │   │
│  │                    │   Log Archive acct)│                      │   │
│  │                    └────────────────────┘                      │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                    │                                    │
│                    ┌───────────────▼───────────────┐                   │
│                    │      SECURITY ACCOUNT          │                   │
│                    │   (Delegated Admin for all     │                   │
│                    │    security services)          │                   │
│                    │                                │                   │
│                    │  ┌──────────────────────────┐ │                   │
│                    │  │   Security Hub           │ │                   │
│                    │  │   (Aggregated findings   │ │                   │
│                    │  │    from all accounts)    │ │                   │
│                    │  └──────────────┬───────────┘ │                   │
│                    │                 │             │                   │
│                    │  ┌──────────────▼───────────┐ │                   │
│                    │  │   EventBridge            │ │                   │
│                    │  │   (Cross-account event   │ │                   │
│                    │  │    bus)                  │ │                   │
│                    │  └──────────────┬───────────┘ │                   │
│                    │                 │             │                   │
│                    │  ┌──────────────▼───────────┐ │                   │
│                    │  │   Lambda Orchestrator    │ │                   │
│                    │  │   (Triage + Route +      │ │                   │
│                    │  │    Remediate)            │ │                   │
│                    │  └──────────────────────────┘ │                   │
│                    └───────────────────────────────┘                   │
│                                    │                                    │
│          ┌─────────────────────────┼─────────────────────────┐         │
│          ▼                         ▼                         ▼         │
│  ┌───────────────┐      ┌──────────────────┐      ┌──────────────────┐ │
│  │  SIEM         │      │  Ticketing       │      │  Dashboard       │ │
│  │  (Splunk/     │      │  (ServiceNow/    │      │  (Grafana/       │ │
│  │   OpenSearch) │      │   Jira)          │      │   QuickSight)    │ │
│  └───────────────┘      └──────────────────┘      └──────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
```

### 7.2 Design: Automated IAM Access Review System

```
┌─────────────────────────────────────────────────────────────────────┐
│              AUTOMATED IAM ACCESS REVIEW SYSTEM                     │
│                                                                     │
│  TRIGGER: EventBridge cron (weekly) or on-demand                   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    DATA COLLECTION                           │   │
│  │                                                              │   │
│  │  Lambda: CollectIAMData                                      │   │
│  │  • list_users (paginated)                                    │   │
│  │  • list_roles (paginated)                                    │   │
│  │  • get_credential_report                                     │   │
│  │  • list_access_keys per user                                 │   │
│  │  • get_access_key_last_used                                  │   │
│  │  • list_attached_user_policies                               │   │
│  │  • list_user_groups → list_attached_group_policies           │   │
│  │  • simulate_principal_policy (effective permissions)         │   │
│  └──────────────────────────────┬───────────────────────────────┘   │
│                                 │                                    │
│  ┌──────────────────────────────▼───────────────────────────────┐   │
│  │                    ANALYSIS ENGINE                            │   │
│  │                                                              │   │
│  │  Lambda: AnalyzeAccess                                       │   │
│  │  • Unused credentials (90+ days)                             │   │
│  │  • Overly permissive policies (wildcard analysis)            │   │
│  │  • Privilege escalation paths                                │   │
│  │  • Orphaned roles (no trust policy principals exist)         │   │
│  │  • Service roles with human-like permissions                 │   │
│  │  • Cross-account trust anomalies                             │   │
│  └──────────────────────────────┬───────────────────────────────┘   │
│                                 │                                    │
│  ┌──────────────────────────────▼───────────────────────────────┐   │
│  │                    RISK SCORING                               │   │
│  │                                                              │   │
│  │  Score = Σ(violation_weight × severity_multiplier)           │   │
│  │                                                              │   │
│  │  Weights:                                                    │   │
│  │  • Admin access: 100                                         │   │
│  │  • Wildcard resource: 50                                     │   │
│  │  • Unused 90+ days: 30                                       │   │
│  │  • No MFA: 40                                                │   │
│  │  • Old access key: 20                                        │   │
│  └──────────────────────────────┬───────────────────────────────┘   │
│                                 │                                    │
│  ┌──────────────────────────────▼───────────────────────────────┐   │
│  │                    REPORTING & ACTION                         │   │
│  │                                                              │   │
│  │  • Store in DynamoDB (historical trending)                   │   │
│  │  • Generate HTML report → S3 → CloudFront                    │   │
│  │  • High-risk findings → Jira tickets (auto-created)          │   │
│  │  • Critical findings → PagerDuty alert                       │   │
│  │  • Weekly summary → Email via SES                            │   │
│  └──────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 8. Behavioral STAR Stories {#behavioral}

### Map Your Experience to STAR Format

**Story 1: Multi-Account Security Implementation (ValueMomentum)**

> **S**: Insurance company with 10+ AWS accounts, no centralized security visibility, manual IAM management, compliance audit coming in 3 months
>
> **T**: Design and implement enterprise-grade security posture across all accounts with automated compliance monitoring
>
> **A**:
> - Implemented Control Tower with AFT for account governance and SCPs
> - Built centralized Security Hub with delegated admin in security account
> - Created Lambda-based automated remediation for Config rule violations
> - Implemented Identity Center with permission sets replacing individual IAM users
> - Built shift-left pipeline with SonarQube + Trivy + OWASP ZAP
>
> **R**: Zero Critical CVEs in production over 3 months, passed compliance audit, reduced new environment setup from 3 days to 2 hours, eliminated all manual IAM operations

**Story 2: Pipeline Security Automation (TruEquations)**

> **S**: FinTech startup deploying to ECS Fargate with manual deployments, no security scanning, developers pushing directly to production
>
> **T**: Build automated, secure CI/CD pipeline with zero-trust deployment model
>
> **A**:
> - Designed GitHub Actions pipeline with OIDC (keyless auth — no long-lived credentials)
> - Integrated Trivy for container scanning, Snyk for SCA, OWASP ZAP for DAST
> - Implemented deployment gates — pipeline fails on Critical/High CVEs
> - Built automated rollback on health check failures
> - Deployed Prometheus + Grafana for Golden Signals monitoring
>
> **R**: 80% reduction in manual deployment effort, zero Critical vulnerabilities in production over 3 months, MTTR reduced from hours to under 15 minutes

---

### Common Behavioral Questions & Answers

**"Tell me about a time you had to make a security decision under pressure."**

> "At TruEquations,