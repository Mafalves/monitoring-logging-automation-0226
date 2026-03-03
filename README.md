# Project 4: Monitoring, Logging, and Automation

Observability, automation, and operational excellence across the full stack from Projects 1–3.

## Overview

- **Metrics:** Prometheus + Grafana (Kubernetes); CloudWatch (AWS)
- **Logs:** Fluent Bit / CloudWatch Logs agent → CloudWatch or Loki
- **Alerting:** Prometheus Alertmanager; Lambda or Python for AWS
- **Automation:** RDS backups; optional EBS snapshots

See [ARCHITECTURE.md](ARCHITECTURE.md) for monitoring scope, data flow, and design decisions.

## Prerequisites

- k3d, kubectl (Project 3 cluster)
- AWS CLI, configured credentials
- Helm (for kube-prometheus-stack)

## Cluster (k3d)

```bash
k3d cluster start dev-cluster    # start
k3d cluster stop dev-cluster     # stop
kubectl get nodes                # verify
```

## AWS

```bash
cd terraform-aws-infra-1025
terraform apply    # bring up EC2, RDS
terraform destroy  # tear down when not using
terraform output   # instance IDs, DB endpoint
```

## Deploy / Access

*(Filled as Tasks 5–6 progress.)*

## Related Projects

| Project | Path |
|---------|------|
| Project 1 – Terraform AWS | `terraform-aws-infra-1025` |
| Project 2 – Docker + CI/CD | `dockerized-app-cicd-aws-1125` |
| Project 3 – Kubernetes | `k8s-app-deployment-0126` |