# Data Warehouse Transition Plan
## Marigold Commercial Business Unit

**Document Version:** 1.1\
**Date:** October 2025  \
**Prepared By:** Friscian Viales - Lead Data Engineer  

---

## Executive Summary

Following Marigold's agreement to divest its Enterprise business unit to Zeta, this document outlines a comprehensive transition plan for separating the unified data warehouse infrastructure into two independent systems. The transition will ensure business continuity for Marigold Commercial while enabling Zeta Enterprise to maintain operational analytics capabilities.

**Key Highlights:**
- **Timeline:** ~3 month transition period
- **Approach:** Short-term redeployment with long-term modernization pathway
- **Business Impact:** Minimal disruption to analytical capabilities during transition
- **Investment Required:** GCP infrastructure, Tableau licensing, engineering resources

---

## 1. Business Context

### 1.1 Transaction Overview
Marigold has completed the sale of its Enterprise business unit to Zeta Global. Under the terms of this agreement:
- Zeta will acquire all Enterprise-related operations, customer relationships, and associated technology assets
- Marigold will retain its Commercial business unit and continue independent operations
- Both parties have agreed to share intellectual property and technical resources during the transition period

### 1.2 Current State
The existing data warehouse serves as the single source of truth for the entire Marigold organization, supporting both Enterprise and Commercial business units through:
- Unified BigQuery data warehouse on Google Cloud Platform
- Integrated data pipelines processing data from multiple sources (Salesforce, AWS, third-party APIs)
- Tableau dashboards serving analytics across all business units
- Luigi-based orchestration framework for ETL processes

### 1.3 Future State Vision
Upon completion of this transition:
- **Zeta Enterprise** will inherit the existing GCP infrastructure, data models, and pipeline codebase
- **Marigold Commercial** will operate an independent data warehouse environment with replicated assets and ongoing analytical capabilities
- Both organizations will maintain data quality, reporting accuracy, and operational insights without interruption

---

## 2. Guiding Principles & Assumptions

### 2.1 Core Principles
1. **Business Continuity First:** Minimize disruption to ongoing analytics and reporting
2. **Clean Separation:** Establish clear boundaries between Enterprise and Commercial data assets
3. **Compliance & Security:** Maintain data governance standards throughout the transition
4. **Knowledge Transfer:** Document all systems and processes for operational handoff
5. **Scalability:** Design new Commercial infrastructure to support future growth

### 2.2 Key Assumptions
- Zeta will inherit all existing architecture, technologies, GCP projects, and codebase currently supporting the data warehouse
- Assuming the existing GCP account will be inherited by Zeta, an account ownership transfer will be required
- Marigold Commercial requires redeployment of data warehouse assets to maintain analytical capabilities. This requires Marigold to get a new GCP account
- Both parties will cooperate on intellectual property sharing and technical resource access during transition
- New Salesforce instance will be provisioned for Marigold Commercial with minimal data model changes (same schema, different connection)
- Netsuite will remain as part of MG Commercial. However, Zeta still needs to define how they are going to setup their billing systems for Enterprise
- Netsuite has recently upgraded their backend systems and the existing integration will no longer work by the beginning of 2026
- AWS resources that are currently owned by CM will likely stay with Commercial. Zeta will be required to create a new AWS account for Enterprise data exports
- Budget approval will be obtained for new GCP account and associated cloud services
- Tableau Dashboards with combined views between Enterprise and Commercial will need to be broken down

---

## 3. Scope & Requirements

### 3.1 Infrastructure Requirements

#### Cloud Platform (Google Cloud Platform)
- [x] New GCP Organization/Account for Marigold Commercial
- [x] BigQuery datasets and data warehouse infrastructure
- [x] Cloud Storage buckets for data lake and staging areas
- [x] Google Kubernetes Engine (GKE) Autopilot cluster for pipeline orchestration
- [x] Cloud Run services for API endpoints and microservices
- [x] Cloud Build for CI/CD automation
- [x] Artifact Registry for container image management
- [x] Service Accounts with appropriate IAM roles and permissions
- [x] Secret Manager for secure credential storage
- [x] Cloud Monitoring and Logging for observability

#### Analytics & Visualization
- [x] Tableau Cloud account for Marigold Commercial
- [x] Migration of existing Commercial-focused dashboards
- [x] User access provisioning and permissions configuration

#### Development & Operations Tools
- [x] GitHub repository access for source code version control
- [x] Development environment setup (PyCharm Community Edition or similar IDE)
- [x] Documentation repository for runbooks and technical specifications

### 3.2 Data Source Requirements
- **Salesforce (SFDC):** New instance for Marigold Commercial with data model alignment
- **AWS Resources:** Identification and migration of Commercial-specific data sources (CM is the current owner of the AWS account, meaning Zeta will require a different AWS account to continue to stage exports from event-stream and other enterprise exports)
- **Third-Party APIs:** Re-authentication and configuration for Commercial environment (Zuora and Netsuite)
- **Historical Data:** Determination of data retention policies and migration scope

### 3.3 Functional Requirements
- Data pipelines must support daily refresh cycles for operational reporting
- Dashboards must maintain existing functionality and KPI calculations
- Data models should preserve historical trend analysis capabilities
- System must support user self-service for basic reporting needs
- Documentation must enable non-technical stakeholders to understand data lineage

---

## 4. Technical Architecture

### 4.1 Data Pipeline Architecture Options

The transition presents an opportunity to evaluate the long-term data pipeline strategy. Three primary options have been identified:

#### Option 1: Luigi Pipelines (Current State)
**Description:** Redeploy existing Luigi-based Python orchestration framework

**Advantages:**
- Minimal learning curve (currently in production use)
- Lightweight and cost-effective operation (~$30-50/month compute costs)
- Battle-tested codebase with known performance characteristics
- Suitable for moderate workloads (current Commercial volume)
- Fastest path to production (1-2 months deployment)

**Disadvantages:**
- Legacy framework with declining community support
- Development velocity constrained by boilerplate code requirements
- Requires technical expertise for pipeline maintenance and troubleshooting
- Limited native UI for monitoring and manual job triggering
- Dependency management can be challenging

**Recommended For:** Short-term solution (6-12 months) to ensure business continuity

---

#### Option 2: Windmill Pipelines (Modernization Path)
**Description:** Migrate to modern orchestration platform with workflow management

**Advantages:**
- Modern, actively maintained framework with regular updates
- Intuitive web UI for pipeline monitoring, triggering, and scheduling
- Reduced code complexity with built-in workflow abstractions
- Better suited for enabling non-technical users to understand data flows
- Integrated secret management and version control
- Self-hosted option maintains infrastructure control

**Disadvantages:**
- Requires code refactoring (~2-3 months engineering effort)
- Moderate operational costs (~$200-300/month including compute and licensing)
- Learning curve for development team
- Migration risk during transition period

**Recommended For:** Long-term sustainable solution (12+ months outlook)

---

#### Option 3: Third-Party Integration Platform (e.g., SnapLogic, Fivetran, Zapier)
**Description:** Adopt SaaS-based data integration platform

**Advantages:**
- No-code/low-code interface accessible to business users
- Managed infrastructure eliminates operational overhead
- Pre-built connectors for common data sources
- Vendor-provided support and SLAs

**Disadvantages:**
- Significant cost increase ($1,000-5,000+/month depending on data volume)
- Requires rebuilding all pipelines from scratch (3-4 months minimum)
- Vendor lock-in and reduced flexibility for custom transformations
- Potential data residency and compliance considerations
- Less control over pipeline optimization

**Recommended For:** Organizations prioritizing business user autonomy over cost efficiency

---

### 4.2 Recommended Approach: Phased Migration

**Phase 1 (Months 1-3): Stabilization**
- Redeploy existing Luigi pipelines to new Marigold GCP account
- Redeploy Tableau dashboards to new environment
- Establish baseline operational capability (Clear separation of resources between MG and Zeta)
- Focus on business continuity and minimal changes

**Phase 2 (Months 4-6): Modernization (Optional)**
- Gradual migration from Luigi to Windmill
- Parallel operation to validate functionality
- Knowledge transfer and training for operations team

---

## 5. Implementation Roadmap

### Phase 1.1: Foundation & Setup
**Objectives:** Establish new infrastructure and project governance

**Key Activities:**
- (MG) Obtain GCP account approval and provision organization
- (MG) Deploy core GCP services (BigQuery, Cloud Storage, GKE, IAM)
- (MG) Set up GitHub repository structure and access controls
- (MG) Migrate pipeline code to new GitHub repository
- (MG) Configure Cloud Build CI/CD workflows
- (MG) Create project documentation framework
- (Zeta) Initiate account transfer process for GCP and Tableau Clout account

**Deliverables:**
- Functional GCP environment with production deployments
- Service accounts and IAM policies documented
- Project charter and stakeholder alignment
- CI/CD automation for code deployments

**Dependencies:** Budget approval, GCP account creation

---

### Phase 1.2: Data Source Migration
**Objectives:** Establish connectivity to Commercial data sources and update Enterprise data sources

**Key Activities:**
- (MG) Configure new Salesforce instance connection (critical path)
- (MG) Rebuild NetSuite pipelines using new backend
- (MG) Migrate API credentials to Secret Manager
- (MG) Test data extraction from all source systems
- (MG) Validate data quality and completeness
- (Zeta) Identify and document AWS resource dependencies
- (Zeta) Configure new AWS S3 buckets and point Enterprise exports to new location

**Deliverables:**
- Operational data ingestion from Salesforce, Netsuite, Zuora and AWS
- Documented data source inventory
- Data quality validation reports
- Connection string templates and configuration guides

**Dependencies:** New SFDC instance provisioning, New AWS S3 bucket creation, New NetSuite integration

---

### Phase 1.3: Analytics & Reporting
**Objectives:** Restore analytical and reporting capabilities

**Key Activities:**
- (MG) Provision Tableau Cloud account for Marigold Commercial
- (MG) Migrate Commercial-focused dashboards from existing environment
- (MG) Reconnect Tableau workbooks to new BigQuery datasets
- (MG) Eliminate dependencies on Enterprise-specific data sources
- (MG) User acceptance testing with business stakeholders
- (MG) Training sessions for dashboard consumers
- (Zeta) Initiate account transfer process for Tableau Cloud account
- (Zeta) Clean up Commercial-specific dashboards
- (Zeta) Eliminate dependencies on Commercial-specific data sources

**Deliverables:**
- Migrated Tableau dashboards with validated metrics
- User access provisioning complete
- Dashboard usage documentation
- Training materials and recorded sessions

---

### Phase 1.4: Knowledge Transfer & Handoff
**Objectives:** Enable independent operations for both organizations

**Key Activities:**
- (Both) Comprehensive documentation review
- (Both) Operational training for support teams
- (Both) Finalize service transition agreements
- (Both) Post-implementation review and lessons learned

**Deliverables:**
- Complete technical documentation repository
- Trained operational support team
- Incident response procedures
- Transition sign-off from stakeholders

---

## 6. Data Model & Migration Considerations

### 6.1 Salesforce Data Model Alignment
**Challenge:** New Salesforce instance may introduce schema variations

**Mitigation Strategy:**
- Engage Salesforce administrator early in transition planning
- Implement a migration-first and improvements-second approach 
- Document existing SFDC object models and field mappings
- Implement data quality checks to detect model drift
- Create mapping documentation between old and new SFDC instances

**Critical Success Factor:** Maintain consistent naming conventions and data types to preserve existing BigQuery models

---

### 6.2 Netsuite Data Migration
**Challenge:** Netsuite backend systems have recently upgraded and the existing integration will no longer work

**Mitigation Strategy:**
- New integration needs to be created in order to ensure data will remain available in the future
- Zeta needs to define whether they will use their own Netsuite account or different billing system. This will impact existing data models that rely on billing information

**Critical Success Factor:** Build new integration before end of the year

---

### 6.3 AWS Data Migration
**Challenge:** AWS account contains S3 buckets with data exports from both Commercial and Enterprise environments.

**Mitigation Strategy:**
- Zeta needs to create a new AWS account for Enterprise data exports
- Alternative path is for Zeta to use another stage area like GCS buckets, however that will require considerate pipeline updates

**Critical Success Factor:** Enable new S3 bucket for Enterprise exports

---

## 7. Risk Assessment & Mitigation

| Risk                                        | Probability | Impact | Mitigation Strategy                                                                          |
|---------------------------------------------|-------------|--------|----------------------------------------------------------------------------------------------|
| **Salesforce data model changes**           | High        | High   | Early engagement with SFDC team; flexible pipeline design; comprehensive testing             |
| **AWS resource access loss**                | Medium      | Medium | Identify dependencies early; secure ongoing access agreements; migrate to GCP where feasible |
| **Knowledge gaps after transition**         | Medium      | Medium | Comprehensive documentation; training sessions; maintain consultant support option           |
| **Extended downtime during migration**      | Low         | High   | Phased approach; parallel operations during transition; off-hours deployment                 |
| **Resource constraints (budget/personnel)** | Medium      | High   | Secure executive sponsorship; define clear scope boundaries; prioritize critical paths       |
| **Timeline delays**                         | Medium      | Medium | Build buffer into timeline; identify critical path items early; regular status reviews       |

---

## 8. Cost Considerations

### Current Infrastructure Costs (Monthly Average Jul-Sep)

**Google Cloud Platform:**
- BigQuery (storage + queries): $300-400
- Cloud Storage: $50-70
- GKE Autopilot: $30-50
- Other services: $40-60
- **Subtotal: $400-600**

**Tableau Cloud:**
- Creator licenses (2-3): TBD
- Explorer licenses (5-10): TBD
- **Subtotal: TBD**

**Pipeline Framework:**
- Luigi (current): $0 licensing + compute included above
- Windmill (future): $200-300/month + compute included above
- Third-party platform: $1,000-5,000/month

**Total Monthly Run Rate:**
- **Short-term (Luigi):** $300-500
- **Long-term (Windmill):** $500-600
- **Third-party alternative:** $1,000-5,000

---

## 9. Success Criteria & Metrics

### 9.1 Technical Success Metrics
- ✅ All data pipelines running on schedule with >99% success rate
- ✅ Data latency within acceptable thresholds (<24 hours for batch processes)
- ✅ Zero critical data quality incidents post-transition
- ✅ All dashboards migrated and validated by business stakeholders
- ✅ System uptime >99% after stabilization period

### 9.2 Business Success Metrics
- ✅ No gaps in reporting availability for critical business KPIs
- ✅ User satisfaction score >4.0/5.0 for analytical capabilities

### 9.3 Operational Success Metrics
- ✅ Documentation completeness score >90% (all systems documented)
- ✅ Team independence achieved (minimal external support required)
- ✅ Infrastructure costs within 10% of projections

---

## 10. Recommendations

### 10.1 Immediate Action Items
1. Secure executive approval and budget allocation
2. Initiate GCP account creation process
3. Begin Salesforce instance provisioning and planning
4. Assemble core transition team and assign roles
5. Early engagement with Zeta stakeholders to identify gaps in data model alignment

### 10.2 Strategic Recommendation
**Adopt a two-phase approach:**

**Phase 1 (Months 1-3):** Deploy existing Luigi-based architecture to new Marigold Commercial GCP environment. This ensures business continuity with minimal risk and allows time for Salesforce stabilization and team knowledge transfer.

**Phase 2 (Months 3-6):** Gradually migrate to Windmill orchestration platform to enable sustainable long-term operations with reduced technical debt and improved accessibility for non-technical stakeholders.

**Rationale:** This balanced approach minimizes transition risk while creating a modernization pathway that improves operational efficiency and reduces dependence on specialized technical knowledge.

### 10.3 Critical Success Factors
- **Early and continuous stakeholder engagement** to manage expectations and surface issues
- **Rigorous testing protocols** including parallel operations to validate data accuracy
- **Comprehensive documentation** to enable operational independence
- **Flexible timeline** with milestone-based progression rather than fixed dates

---

## 12. Conclusion

The separation of Marigold's data warehouse infrastructure represents a significant but manageable technical initiative. By following this structured transition plan, Marigold Commercial can establish an independent, reliable data warehouse that supports ongoing analytical needs while positioning the organization for future growth.

The recommended phased approach balances immediate business continuity requirements with longer-term operational efficiency objectives. Success will require committed executive sponsorship, dedicated engineering resources, and close collaboration between technical and business stakeholders.

With disciplined execution and proactive risk management, this transition can be completed within the target 3-6 month timeline while maintaining uninterrupted access to business-critical analytics and reporting capabilities.

---

## Appendices

### Appendix A: Technical Stack Reference
- **Cloud Platform:** Google Cloud Platform (GCP)
- **Data Warehouse:** BigQuery
- **Orchestration:** Luigi (short-term), Windmill (long-term)
- **Visualization:** Tableau Cloud
- **Version Control:** GitHub
- **CI/CD:** Cloud Build
- **Languages:** Python 3.9+, SQL

### Appendix B: Glossary
- **ETL:** Extract, Transform, Load (data pipeline pattern)
- **GCP:** Google Cloud Platform
- **GKE:** Google Kubernetes Engine
- **IAM:** Identity and Access Management
- **SFDC:** Salesforce
- **UAT:** User Acceptance Testing

### Appendix C: Contact Information
- **Data Warehouse Team:** Ryan Whitehouse, Friscian Viales, Sai Rediboyina
- **SFDC Team:** Ryan Whitehouse, Pinal Kakadiya
- **AWS Team:** Rio Delfin
- **Billing Team (Netsuite & Zuora):** Ryan Whitehouse, Erica Biehl, Jocelyn May
- **CM Team:** Mick Proust, Prasannaa Ganesan
- **Emma Team:** Ev Rumsey

### Appendix D: Document Change History
| Version | Date              | Author                | Changes                                                                                                                |
|---------|-------------------|-----------------------|------------------------------------------------------------------------------------------------------------------------|
| 1.0     | October 20th 2025 | Data Engineering Team | Initial proposal                                                                                                       |
| 1.1     | October 23rd 2025 | Data Engineering Team | Added more details after initial team review. Updated 2.2 key assumtions. Updated section 5 with owners for each task. |

---

**Next Steps:** Schedule kickoff meeting with steering committee to review proposal and obtain formal approval to proceed.