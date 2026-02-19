## Project 4 – Monitoring, Logging, and Automation: Task Plan & Timelines

This document breaks Project 4 into concrete, time-bound tasks. Assume roughly **1–2 hours per day, 5 days per week**; adjust pacing as needed.

**Goal (from Roadmap):** Demonstrate **observability, automation, and operational excellence** across the full stack built in Projects 1–3.

---

## Week 1 – Foundations and Architecture (Steps 1–4)

**Goal:** Know exactly what you’re monitoring, where logs go, and which tools you’ll use.

### Task 1: Clarify the Monitoring Scope (≈ 0.5 day)
- List all targets to monitor:
  - **Kubernetes** (Project 3): Flask app pods, cluster health, HPA behavior.
  - **AWS** (Project 1): EC2 instances, RDS, optional S3.
- Decide which components are in-scope vs out-of-scope for this project.
- Write a short `ARCHITECTURE.md` or `monitoring-design-notes.md` describing:
  - What you will monitor and why.
  - How this builds on Projects 1–3.
  - High-level data flow: sources → collectors → storage → dashboards/alerts.

### Task 2: Log Collection Strategy (≈ 0.5 day)
- Choose one approach:
  - **Option A – CloudWatch Logs:** Simpler; integrates with AWS. Use CloudWatch Logs agent on EC2; for K8s, use Fluent Bit/Fluentd to ship container logs to CloudWatch.
  - **Option B – ELK stack:** More flexible querying; self-hosted. Elasticsearch + Logstash/Fluentd + Kibana.
- Document the decision and rationale (cost, complexity, portfolio value).
- **Recommendation for portfolio:** CloudWatch for AWS-native simplicity; add Loki + Grafana for K8s logs if you want a unified Grafana experience.

### Task 3: Metrics Stack Decision (≈ 0.5 day)
- **Prometheus + Grafana** (Roadmap requirement):
  - Prometheus scrapes metrics from K8s (kube-state-metrics, metrics-server, app `/metrics` if exposed).
  - Grafana visualizes Prometheus data and optionally CloudWatch.
- Decide where to run Prometheus/Grafana:
  - **In-cluster (k3d):** Easiest for K8s metrics; matches Project 3 setup.
  - **Standalone:** If you prefer separation.
- Capture this in `monitoring-design-notes.md`.

### Task 4: Environment Check and Prerequisites (≈ 0.5 day)
- Confirm Project 3’s k3d cluster is available (or recreate it).
- Confirm Project 1’s AWS infrastructure (EC2, RDS) is reachable.
- Install any local tools needed (e.g., `promtool`, `amtool` for Prometheus/Alertmanager).
- Document how to start/stop the cluster and access AWS resources.

---

## Week 2 – Metrics and Dashboards (Steps 5–9)

**Goal:** Prometheus scraping metrics; Grafana displaying dashboards.

### Task 5: Deploy Prometheus on Kubernetes (≈ 1 day)
- Deploy Prometheus in the k3d cluster (Project 3’s cluster).
- Options:
  - **kube-prometheus-stack (Helm):** Full stack (Prometheus, Grafana, Alertmanager, node-exporter, kube-state-metrics). Recommended for completeness.
  - **Manual manifests:** Prometheus Deployment + Service + ConfigMap for `prometheus.yml`.
- Configure `prometheus.yml` to scrape:
  - Kubernetes API (pods, nodes, services).
  - kube-state-metrics (if not using full stack).
  - Flask app (if you add a `/metrics` endpoint; optional for later).
- Verify: Prometheus UI shows targets as UP.

### Task 6: Deploy Grafana (≈ 0.5 day)
- Deploy Grafana (in-cluster or as part of kube-prometheus-stack).
- Expose Grafana via Ingress or port-forward.
- Set up Prometheus as a data source in Grafana.
- Verify: You can run a simple PromQL query in Grafana.

### Task 7: Configure Prometheus Scraping for the Flask App (≈ 0.5 day, optional)
- If the Flask app does not expose `/metrics`:
  - Add a minimal Prometheus client (e.g., `prometheus_client` in Python) and a `/metrics` route.
  - Rebuild and redeploy the Flask image.
- Add a scrape config in Prometheus for the Flask app Service.
- Verify: Flask metrics appear in Prometheus.

### Task 8: Create Basic Grafana Dashboards (≈ 1 day)
- Create at least two dashboards:
  - **Kubernetes overview:** Pod count, CPU/memory usage, HPA status.
  - **Flask app:** Request rate, error rate, pod restarts (if metrics available).
- Use existing community dashboards (e.g., Kubernetes cluster monitoring) as a starting point.
- Document how to access dashboards and what each shows.

### Task 9: CloudWatch Metrics (Optional, ≈ 0.5 day)
- If EC2 from Project 1 is in scope: ensure CloudWatch agent or default EC2 metrics are available.
- Add CloudWatch as a data source in Grafana (requires IAM/credentials).
- **Trade-off:** Adds AWS integration; skip if focusing on K8s-only for now.

---

## Week 3 – Log Collection (Steps 10–12)

**Goal:** Logs from K8s and/or EC2 flowing to a central store; basic querying.

### Task 10: Log Collection for Kubernetes (≈ 1–1.5 days)
- Deploy a log shipper in the cluster:
  - **Fluent Bit** or **Fluentd** as a DaemonSet (collects logs from all nodes).
  - Destination: CloudWatch Logs or Loki (if using Grafana for logs).
- Configure log groups/streams or Loki labels.
- Verify: Logs from the Flask app pods appear in the chosen destination.

### Task 11: Log Collection for EC2 (≈ 0.5–1 day)
- If EC2 is in scope: install and configure the CloudWatch Logs agent on EC2 instances.
- Ship application logs and/or system logs to CloudWatch Logs.
- **Alternative:** If EC2 runs the same Flask app via Docker, ensure container logs are captured.
- Verify: Logs appear in CloudWatch Logs.

### Task 12: Verify End-to-End Log Flow (≈ 0.5 day)
- Generate some log entries (e.g., hit Flask routes, trigger errors).
- Confirm logs are searchable and viewable in CloudWatch or Kibana/Loki.
- Document the log flow in `ARCHITECTURE.md` or a dedicated `LOGGING.md`.

---

## Week 4 – Alerting, Automation, and Polish (Steps 13–17)

**Goal:** Alerts fire on thresholds; backups run automatically; documentation is complete.

### Task 13: Define Alert Rules (≈ 0.5 day)
- Add Prometheus alert rules (e.g., pod down, high CPU, high memory).
- Configure Alertmanager (if using kube-prometheus-stack) for routing.
- Decide on notification channel: email, Slack, or a simple webhook for now.
- Document alert rules and their purpose.

### Task 14: Implement an Alert Action (Python or Lambda) (≈ 1 day)
- **Option A – Lambda:** Create a Lambda function triggered by CloudWatch Alarm (e.g., EC2 CPU > 80%). Lambda can send SNS, post to Slack, or log to S3.
- **Option B – Python script:** A script that queries Prometheus or CloudWatch, evaluates thresholds, and sends notifications (e.g., via `requests` to a webhook).
- **Recommendation:** Lambda for AWS-native, event-driven; Python script for Prometheus-based or more custom logic.
- Verify: Trigger the condition and confirm the alert fires.

### Task 15: Automated Backups or Snapshots (≈ 1–1.5 days)
- **RDS:** Create an automated backup schedule (Terraform `backup_retention_period`, or EventBridge + Lambda to trigger snapshots).
- **EBS:** Optional – EventBridge rule + Lambda to create EBS snapshots for EC2 volumes on a schedule.
- Document the backup schedule, retention, and how to restore.

### Task 16: Optional – Python Script for Metric-Based Scaling (≈ 0.5 day)
- If time allows: a Python script that reads Prometheus or CloudWatch metrics and scales a Deployment or ASG based on custom logic.
- **Trade-off:** HPA already handles CPU-based scaling; this demonstrates custom automation. Skip if scope is tight.

### Task 17: Documentation and Flow Diagrams (≈ 1 day)
- Create monitoring and alerting flow diagrams (sources → collectors → storage → dashboards → alerts).
- Write or refine the main Project 4 `README` to include:
  - Prerequisites (k3d, kubectl, AWS CLI, etc.).
  - How to deploy the monitoring stack.
  - How to access Grafana and dashboards.
  - How alerts and backups work.
  - Key design decisions and trade-offs.

---

## Summary: Deliverables Checklist

| Deliverable | Task(s) |
|-------------|---------|
| Monitoring and alerting flow diagrams | 1, 17 |
| Prometheus + Grafana deployed and configured | 5, 6, 7, 8 |
| Log collection (K8s and/or EC2) | 10, 11, 12 |
| Alert rules + alert action (Lambda or Python) | 13, 14 |
| Automated backups (RDS, optional EBS) | 15 |
| README explaining automated workflows | 17 |

---

## Dependencies on Previous Projects

| Project | What Project 4 Uses |
|---------|---------------------|
| **Project 1** | EC2, RDS, AWS account for CloudWatch, backups |
| **Project 2** | Flask app Docker image (for `/metrics` if added) |
| **Project 3** | k3d cluster, Flask app Deployment, Ingress |

---

## Tool Choices Quick Reference

| Area | Recommended | Alternative |
|------|-------------|-------------|
| Metrics | Prometheus + Grafana | CloudWatch only |
| Logs (K8s) | Fluent Bit → CloudWatch or Loki | ELK stack |
| Logs (EC2) | CloudWatch Logs agent | ELK (Fluentd) |
| Alerting | Prometheus Alertmanager + Lambda/Python | CloudWatch Alarms only |
| Backups | RDS automated backups, Lambda for EBS | Manual snapshots |
