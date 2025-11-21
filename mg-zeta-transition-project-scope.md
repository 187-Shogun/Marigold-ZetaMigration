# MG-Zeta Data Warehouse Separation - Project Scope

**Project Period:** November 17, 2025 - March 31, 2026\
**Prepared By:** Friscian Viales\
**Version:** 1.1

---

## Executive Summary

This project separates the unified data warehouse infrastructure into two independent systems:
one for Marigold Commercial (new GCP project: `mg-dw-workbench`) and one for Zeta Enterprise
(existing GCP project: `globalbiworkbench`). The approach replicates existing architecture with 
data segregation, ensuring business continuity for both organizations.

---

## Project Objectives

1. Deploy independent data warehouse infrastructure for Marigold Commercial in new GCP project
2. Replicate existing architecture (BigQuery, GCS, GKE, Luigi pipelines)
3. Separate data sources to serve only Marigold-relevant data
4. Migrate Tableau dashboards for Commercial analytics
5. Complete transition within contract period with minimal business disruption

---

## Scope

### In Scope
- New GCP project setup and configuration (`mg-dw-workbench`)
- BigQuery datasets and data models for Commercial data
- GCS buckets for data lake and staging
- GKE Autopilot cluster deployment for pipeline orchestration
- Luigi pipeline redeployment
- Data source connections: Salesforce, NetSuite, Zuora, AWS S3
- Tableau Cloud account setup and dashboard migration
- CI/CD pipeline setup via Cloud Build
- IAM, Secret Manager, and monitoring configuration
- Documentation and knowledge transfer

---

## Timeline Overview

**Total Duration:** 4 months (19 weeks)

### Phase 1: Foundation (Weeks 1-3 / 20251117-20251205) 
- GCP project provisioning and base infrastructure deployment
- GitHub repository setup
- Service accounts and IAM configuration
- CI/CD pipeline deployment

### Phase 2: Staging Layer (Weeks 4-6 / 20251208-20251226)
- BigQuery STG dataset creation
- Data source connections (Salesforce, NetSuite, Zuora, AWS)
- Luigi pipeline deployment and testing
- Stage table deployment
- Data validation and quality checks

### Phase 3: Analytics Layer (Weeks 7-11 / 20251229-20260130)
- BigQuery ODS & EDW dataset creation
- dbt models deployment
- Tableau Cloud account provisioning
- Dashboard migration and reconnection to new EDW models
- Stakeholder validation period (Incremental and In-Parallel)

### Phase 4: Validation & Stabilization (Weeks 12-15 / 20260202-20260227)
- Final data validation
- Production cutover
- Monitoring and issue resolution
- Project closeout and handoff

### Phase 5: Buffer (Weeks 16-20 / 20260302-20260331)
- Additional time buffer to account for unforeseen risks and blockers

---

## Key Deliverables

1. **Infrastructure**
   - Functional GCP environment with all services deployed
   - Operational data pipelines running on schedule
   - Monitoring and alerting configured

2. **Data Assets**
   - BigQuery datasets with Commercial data
   - Validated data quality and completeness
   - Working connections to all data sources

3. **Analytics**
   - Migrated Tableau dashboards
   - User access provisioned
   - Dashboard documentation

4. **Documentation**
   - Architecture documentation
   - Runbooks and operational procedures
   - Data lineage and source documentation
   - Training materials

---

## Critical Dependencies

### External Dependencies
- **Salesforce:** New instance provisioning and configuration (MG team)
- **NetSuite:** New integration setup due to backend upgrade (MG team)
- **AWS:** S3 bucket access for Commercial data exports (Zeta team)
- **Tableau:** Cloud account procurement (MG team)

### Technical Dependencies
- Salesforce schema alignment with existing models
- AWS S3 bucket structure and access patterns to remain unchanged

---

## Success Criteria
- Track progress on a daily and weekly basis
- Monitor adherence to timelines
- 80% of migration completed by the end of January 2026

## Resource Requirements

### GCP Services
- BigQuery (storage + compute)
- Cloud Storage
- GKE Autopilot
- Cloud Build
- Secret Manager
- Cloud Monitoring

### External Services
- Tableau Cloud (Creator + Explorer licenses)
- GitHub Account

### Communication
- Weekly status updates every Monday
- Slack channel for day-to-day coordination
- Issue tracking via GitHub

## Assumptions

1. Existing GCP project will be transferred to Zeta ownership (Potentially under sailthru.com organization)
2. Marigold will establish new GCP project (`mg-dw-workbench`)
4. New Salesforce instance will maintain schema compatibility for plug-and-play migration of ETL pipelines
5. NetSuite remains with Marigold Commercial
6. Zeta yet to define how they are going to manage billing data
7. AWS resources currently owned by CM remain with Commercial
8. Zeta yet to define how they will continue to export CD usage data (Potentially to new AWS account)
