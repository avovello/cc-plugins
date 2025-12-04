---
description: Automate deployment workflows with CI/CD pipelines, monitoring, and rollback capabilities
---

# Deploy Command

**Purpose**: Automate deployment workflows with CI/CD pipelines, monitoring, and rollback capabilities

## Workflow

### Step 1: Determine Deployment Goal
- Setup CI/CD pipeline
- Deploy to environment (staging/production)
- Configure monitoring/alerting
- Implement feature flags
- Setup infrastructure

### Step 2: Analyze Current State
- Detect existing deployment setup
- Identify gaps (no CI/CD, no monitoring, etc.)
- Assess infrastructure needs

### Step 3: Create Deployment Plan
Run **deployment-strategist** agent:
- Choose deployment strategy (blue-green, canary, rolling)
- Plan CI/CD pipeline stages
- Design monitoring setup
- Create rollback plan

### Step 4: Implement Infrastructure
Launch agents in parallel:
1. **ci-cd-configurator** - Setup GitHub Actions/GitLab CI/Jenkins
2. **infrastructure-coder** - Create Terraform/CloudFormation
3. **monitoring-configurator** - Setup Datadog/Grafana/Prometheus

### Step 5: Deploy
Run **deployment-validator** agent:
- Execute deployment
- Run health checks
- Verify monitoring
- Test rollback mechanism

### Step 6: Monitor
- Real-time deployment monitoring
- Automatic rollback on failure
- Success confirmation

## Usage Examples

```bash
/deploy "Setup GitHub Actions CI/CD"
/deploy "Deploy to staging"
/deploy "Setup monitoring for production"
/deploy "Implement feature flags"
/deploy production
```

## Agents (7)

1. **ci-cd-configurator** - Setup CI/CD pipelines
2. **infrastructure-coder** - Create IaC (Terraform, CloudFormation)
3. **monitoring-configurator** - Setup monitoring/alerting
4. **deployment-strategist** - Plan deployment strategy
5. **rollback-planner** - Plan rollback mechanisms
6. **feature-flag-implementer** - Implement feature flags
7. **deployment-validator** - Validate deployment health

## Deployment Strategies

**Blue-Green**: Two identical environments, switch between them
**Canary**: Gradual rollout to subset of users
**Rolling**: Update instances one by one
**Feature Flags**: Deploy dark, enable gradually

## Output

```
deploy-output/
├── DEPLOYMENT_PLAN.md
├── ci-cd-config.yml
├── infrastructure.tf
├── monitoring-config.yml
└── ROLLBACK_PROCEDURE.md
```
