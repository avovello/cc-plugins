# Deploy Plugin 🚢

CI/CD pipeline setup, deployment automation, monitoring configuration, and rollback strategies.

## Overview

The Deploy plugin automates deployment workflows that Feature plugin doesn't address - CI/CD setup, infrastructure configuration, monitoring, and production deployment.

## Use Cases

- **CI/CD Setup**: GitHub Actions, GitLab CI, Jenkins
- **Infrastructure**: Terraform, CloudFormation, Kubernetes
- **Monitoring**: Datadog, Grafana, Prometheus, CloudWatch
- **Deployment**: Blue-green, canary, rolling deployments
- **Feature Flags**: LaunchDarkly, Unleash, custom flags
- **Rollback**: Automated rollback on failure

## Usage

```bash
/deploy "Setup GitHub Actions CI/CD"
/deploy "Deploy to staging"
/deploy "Setup production monitoring"
/deploy production
```

## Agents (7)

1. **ci-cd-configurator** - Setup pipelines
2. **infrastructure-coder** - Create IaC
3. **monitoring-configurator** - Setup monitoring
4. **deployment-strategist** - Plan strategy
5. **rollback-planner** - Rollback mechanisms
6. **feature-flag-implementer** - Feature flags
7. **deployment-validator** - Validate health

## Deployment Strategies

- **Blue-Green**: Zero downtime, instant rollback
- **Canary**: Gradual rollout, early issue detection
- **Rolling**: Update instances incrementally
- **Feature Flags**: Deploy dark, enable gradually

## Priority

**P1 - HIGH** - Modern teams need deployment automation
