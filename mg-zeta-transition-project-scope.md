# MG-Zeta Data Warehouse Separation - Project Scope

**Project Period:** November 17, 2025 - March 31, 2026 (4.5 months)  
**Prepared By:** Shogun (Lead Consultant)  
**Version:** 1.0

## Executive Summary

This project separates the unified data warehouse infrastructure into two independent systems: one for Marigold Commercial (new GCP project: `mg-dw-workbench`) and one for Zeta Enterprise (existing GCP project: `globalbiworkbench`). The approach replicates existing architecture with data segregation, ensuring business continuity for both organizations.

## Project Objectives

1. Deploy independent data warehouse infrastructure for Marigold Commercial in new GCP project
2. Replicate existing architecture (BigQuery, GCS, GKE, Luigi pipelines)
3. Separate data sources to serve only Marigold-relevant data
4. Migrate Tableau dashboards for Commercial analytics
5. Complete transition within contract period with minimal business disruption

## Scope

### In Scope
- New GCP project setup and configuration (`mg-dw-workbench`)
- BigQuery datasets and data models for Commercial data
- GCS buckets for data lake and staging
- GKE Autopilot cluster deployment for pipeline orchestration
- Luigi pipeline redeployment (existing framework, no modernization)
- Data source connections: Salesforce, NetSuite, Zuora, AWS S3
- Tableau Cloud account setup and dashboard migration
- CI/CD pipeline setup via Cloud Build
- IAM, Secret Manager, and monitoring configuration
- Documentation and knowledge transfer

### Out of Scope
- Luigi to Windmill migration (future phase)
- Architecture modernization or optimization
- Data model changes or improvements
- Zeta Enterprise infrastructure work (handled by Zeta team)
- Historical data beyond agreed retention period
- Third-party integration platform evaluation

## Timeline Overview

**Total Duration:** 4.5 months (18 weeks)

### Phase 1: Foundation (Weeks 1-4)
- GCP project provisioning and base infrastructure
- GitHub repository setup
- Service accounts and IAM configuration
- CI/CD pipeline deployment

### Phase 2: Data Infrastructure (Weeks 5-10)
- BigQuery dataset creation and schema deployment
- Data source connections (Salesforce, NetSuite, Zuora, AWS)
- Luigi pipeline deployment and testing
- Data validation and quality checks

### Phase 3: Analytics Layer (Weeks 11-14)
- Tableau Cloud account provisioning
- Dashboard migration and reconnection
- User access and training
- Parallel validation period

### Phase 4: Cutover & Stabilization (Weeks 15-18)
- Final data validation
- Production cutover
- Monitoring and issue resolution
- Project closeout and handoff

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

## Critical Dependencies

### External Dependencies
- **Salesforce:** New instance provisioning and configuration (MG team)
- **NetSuite:** New integration setup due to backend upgrade (MG team)
- **AWS:** S3 bucket access for Commercial data exports (Zeta team)
- **Tableau:** Cloud account procurement (MG team)
- **GCP:** Budget approval and account setup (MG team)

### Technical Dependencies
- Access to existing codebase and documentation
- Zeta team cooperation for account transfer planning
- Salesforce schema alignment with existing models
- AWS S3 bucket structure and access patterns

## Risk Assessment

| Risk                          | Probability | Impact | Mitigation                                                              |
|-------------------------------|-------------|--------|-------------------------------------------------------------------------|
| Salesforce data model changes | High        | High   | Early SFDC team engagement; schema validation; flexible pipeline design |
| NetSuite integration delays   | High        | High   | Prioritize NetSuite rebuild; establish fallback plan                    |
| AWS resource access issues    | Medium      | Medium | Document dependencies early; secure access agreements                   |
| Timeline compression          | Medium      | High   | Focus on critical path; parallel workstreams where possible             |
| Knowledge gaps                | Medium      | Medium | Comprehensive documentation; training sessions                          |
| Data quality issues           | Low         | High   | Rigorous testing; parallel validation period                            |

## Success Criteria

### Technical Metrics
- All data pipelines operational with >99% success rate
- Data latency <24 hours for batch processes
- Zero critical data quality incidents post-cutover
- System uptime >99% after stabilization

### Business Metrics
- No gaps in Commercial reporting capabilities
- All critical dashboards migrated and validated
- User acceptance achieved

### Operational Metrics
- Documentation complete (>90% coverage)
- Team operational independence
- Infrastructure costs within projections

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
- GitHub Enterprise

### Personnel
- Lead Consultant (full-time)
- Data Engineering support (as needed)
- SFDC team coordination
- Business stakeholder engagement

## Budget Considerations

**Estimated Monthly Run Rate (post-deployment):**
- GCP Infrastructure: $400-600/month
- Tableau Cloud: TBD (based on license count)
- **Total:** $400-600/month (excluding Tableau)

## Project Organization

### Steering Committee
- Marigold stakeholders
- Zeta coordination contacts
- Data Engineering leadership

### Core Team
- Lead Consultant (Shogun)
- Data Engineering Team (Ryan Whitehouse, Friscian Viales, Sai Rediboyina)
- SFDC Team (Ryan Whitehouse, Pinal Kakadiya)
- AWS Team (Rio Delfin)
- Billing Team (Ryan Whitehouse, Erica Biehl, Jocelyn May)

### Communication
- Weekly status updates
- Bi-weekly steering committee check-ins
- Slack channel for day-to-day coordination
- Issue tracking via GitHub

## Assumptions

1. Existing GCP project will be transferred to Zeta ownership
2. Marigold will establish new GCP organization/account
3. Both parties will cooperate on IP sharing during transition
4. New Salesforce instance will maintain schema compatibility
5. NetSuite remains with Marigold Commercial
6. AWS resources currently owned by CM remain with Commercial
7. Budget approval obtained for new infrastructure
8. Zeta will establish separate AWS account for Enterprise exports

## Next Steps

1. Secure executive approval and budget allocation
2. Initiate GCP account creation
3. Begin Salesforce instance planning
4. Schedule project kickoff meeting
5. Establish communication channels and cadence

---

**Document Status:** Draft v1.0  
**Next Review:** Post-kickoff meeting