# Project 4: Monitoring, Logging, and Automation

Demonstrate **observability, automation, and operational excellence** across the full stack from Projects 1–3.

## Goal (from Roadmap)

- **Log collection:** CloudWatch or ELK stack
- **Metrics and dashboards:** Prometheus + Grafana
- **Alerting:** Python or Lambda-based alerts (e.g., EC2 CPU threshold)
- **Automation:** Automated backups or snapshots; optional Python scripts for metric-based scaling

## Task Plan

See [project4-task-plan.md](project4-task-plan.md) for the detailed, week-by-week task breakdown.

## Dependencies

| Project | Path | What Project 4 Uses |
|---------|------|--------------------|
| Project 1 | `terraform-aws-infra-1025` | EC2, RDS, AWS for CloudWatch, backups |
| Project 2 | `dockerized-app-cicd-aws-1125` | Flask app image (for `/metrics` if added) |
| Project 3 | `k8s-app-deployment-0126` | k3d cluster, Flask Deployment, Ingress |

## Status

- [ ] Week 1 – Foundations and architecture
- [ ] Week 2 – Metrics and dashboards (Prometheus + Grafana)
- [ ] Week 3 – Log collection
- [ ] Week 4 – Alerting, automation, and polish
