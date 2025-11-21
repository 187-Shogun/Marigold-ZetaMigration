# MG-Zeta Data Warehouse Separation - Detailed Task Roadmap

**Engineer:** Friscian Viales (Single resource - 15 hrs/week)
**Project Period:** November 17, 2025 - March 31, 2026
**Critical Constraint:** NetSuite integration must complete by December 31, 2025
**Primary Goal:** All building/migration complete by January 31, 2026

---

## Phase 1: Foundation & Infrastructure Setup
**Duration:** Weeks 1-3 (Nov 17 - Dec 5, 2025)
**Objective:** Establish new GCP infrastructure and development environment

### 1.1 GCP Core Infrastructure

#### GCP Project & Organization Setup
- [ ] Create new GCP project `mg-dw-workbench`
- [ ] Configure billing account and link to project
- [ ] Enable required GCP APIs (BigQuery, GKE, Cloud Build, Secret Manager, etc.)

#### BigQuery Infrastructure
- [ ] Create `STG` dataset (staging layer)
  - Configure US multi-region location
  - Configure dataset-level permissions
- [ ] Create `ODS` dataset (operational data store)
  - Configure dataset-level permissions
- [ ] Create `EDW` dataset (enterprise data warehouse)
  - Configure dataset-level permissions
- [ ] Create `DEV` dataset for development/testing
- [ ] Document dataset naming conventions and purpose

#### Google Cloud Storage (GCS)
- [ ] Create `mg-dw-datalake` bucket
  - Configure IAM permissions
- [ ] Document bucket naming conventions and usage

#### Google Kubernetes Engine (GKE)
- [ ] Deploy GKE Autopilot cluster
  - Set up cluster autoscaling parameters
  - Configure cluster networking (VPC-native)
- [ ] Configure GKE workload identity
  - Link Kubernetes service accounts to GCP service accounts
- [ ] Set up kubectl access
  - Configure kubeconfig
  - Test cluster connectivity
- [ ] Create namespaces
  - `luigi` namespace for luigi pipelines
- [ ] Document cluster architecture and access procedures

#### Cloud Build & Artifact Registry
- [ ] Create Artifact Registry repositories
  - `mg-dw-docker-images` for container images
  - Set up retention policies
- [ ] Configure Cloud Build service account permissions
- [ ] Create Cloud Build trigger templates (will connect after GitHub setup)
- [ ] Set up build artifact retention policies
- [ ] Document build and deployment procedures

#### Secret Manager
- [ ] Set up Secret Manager structure
  - Create secret naming convention (e.g., `sfdc-username`)
- [ ] Create placeholder secrets for:
  - Salesforce credentials
  - NetSuite credentials
  - Zuora API keys
  - AWS S3 access keys (CM and Emma)
- [ ] Configure secret access IAM policies
- [ ] Set up secret rotation policies (where applicable)
- [ ] Document secret management procedures

#### IAM & Service Accounts
- [ ] Create service accounts
  - `luigi-pipeline-sa` for pipeline orchestration
  - `dbt-runner-sa` for dbt executions
- [ ] Configure service account IAM roles
  - BigQuery Data Editor for pipeline accounts
  - Storage Object Admin for data lake access
  - Secret Manager Secret Accessor for credentials
  - Kubernetes Engine Developer for deployment accounts
- [ ] Set up workload identity bindings for GKE

### 1.2 Version Control & Code Repository Setup

#### GitHub Repository Structure
- [ ] Create new GitHub organization or use existing
- [ ] Create repository: `mg-dw-luigi-scheduler`
  - For Luigi pipelines scheduler
  - Set up branch protection (main branch requires PR)
- [ ] Create repository: `mg-dw-dbt`
  - For dbt models and transformations
  - Set up branch protection
- [ ] Create repository: `mg-dw-luigi-sfdc`
  - For SFDC luigi pipelines
  - Document infrastructure components
- [ ] Create repository: `mg-dw-luigi-netsuite`
  - For NeuSuite luigi pipelines
  - Document infrastructure components 
- [ ] Create repository: `mg-dw-luigi-zuora`
  - For Zuora luigi pipelines
  - Document infrastructure components
- [ ] Create repository: `mg-dw-luigi-s3`
  - For S3 luigi pipelines
  - Document infrastructure components
- [ ] Create repository: `mg-dw-docs`
  - For documentation, runbooks, architecture diagrams
- [ ] Document repository structure and contribution guidelines

#### Code Migration & Cleanup
- [ ] Clone existing pipeline code from old repository
- [ ] Remove Enterprise-specific pipeline code
  - Delete Enterprise brand-specific tasks
  - Remove Zeta-related data extractions
  - Clean up deprecated code
- [ ] Update pipeline configuration files
  - Update GCP project IDs
  - Update dataset names
  - Update GCS bucket paths
  - Update service account references
- [ ] Update pipeline connection strings
  - Parameterize environment-specific configs
  - Move hardcoded credentials to Secret Manager references
- [ ] Set up .gitignore properly (exclude secrets, local configs)
- [ ] Commit initial cleaned codebase
- [ ] Document code structure and module organization

#### CI/CD Pipeline Setup
- [ ] Design CI/CD workflow
  - Define deployment triggers (merge to main, tags, manual)
- [ ] Create Cloud Build configuration files
  - `cloudbuild-pipeline.yaml` for Luigi deployment
  - `cloudbuild-dbt.yaml` for dbt deployment
  - `cloudbuild-test.yaml` for testing
- [ ] Connect GitHub repositories to Cloud Build
  - Set up build triggers for each repo
  - Configure trigger conditions (branch, path filters)
- [ ] Test CI/CD pipeline end-to-end
  - Trigger test deployment to staging namespace
  - Verify automated build and deployment
- [ ] Document CI/CD workflows and troubleshooting

### 1.3 Development Environment Setup

#### Local Development Tools (For New Users)
- [ ] Set up local Python environment
  - Python 3.9+ installation
  - Virtual environment setup
  - Install Luigi, dbt, and dependencies
- [ ] Configure gcloud CLI
  - Authenticate with new GCP project
  - Set default project and region
  - Test BigQuery and GCS access
- [ ] Configure kubectl
  - Connect to GKE cluster
  - Test pod access and logs retrieval
- [ ] Install and configure dbt locally
  - Set up dbt profiles.yml for new project
  - Test dbt connection to BigQuery
- [ ] Set up IDE (PyCharm or VSCode)
  - Configure project structure
  - Set up linters (pylint, black, flake8)
  - Configure debugger for Luigi tasks
- [ ] Create local testing environment
  - Local PostgreSQL for Luigi state (if needed)
  - Docker Compose setup for local testing
- [ ] Document local development setup procedures

#### Documentation Infrastructure
- [ ] Create documentation structure
  - `docs/architecture/` - Architecture diagrams and decisions
  - `docs/runbooks/` - Operational procedures
  - `docs/data-sources/` - Data source documentation
  - `docs/pipelines/` - Pipeline documentation
  - `docs/dashboards/` - Tableau dashboard documentation
- [ ] Create initial architecture diagram (Lucidchart/Draw.io)
  - Show GCP infrastructure components
  - Show data flow from sources to Tableau
  - Show security boundaries
- [ ] Document deployment procedures
  - How to deploy pipelines
  - How to deploy dbt models
  - How to rollback
- [ ] Set up documentation review process

### 1.4 Phase 1 Validation & Sign-off

- [ ] **Validation Checklist:**
  - [ ] All GCP services deployed and accessible
  - [ ] All service accounts created with proper permissions
  - [ ] GitHub repositories set up with proper structure
  - [ ] CI/CD pipelines successfully tested
  - [ ] GKE cluster operational and accessible
  - [ ] BigQuery datasets created and accessible
  - [ ] GCS buckets created with proper lifecycle policies
  - [ ] Secret Manager configured with placeholder secrets
  - [ ] Monitoring dashboards visible and functional
  - [ ] Local development environment configured and tested
- [ ] Document any deviations from plan
- [ ] Create Phase 1 completion report
- [ ] **Phase 1 Sign-off:** Self-review and readiness for Phase 2

---

## Phase 2: Staging Layer & Data Source Connections
**Duration:** Weeks 4-6 (Dec 8 - Dec 26, 2025)
**Critical:** NetSuite integration MUST complete by Dec 31, 2025
**Note:** Holiday PTO on Dec 24, 25, 31, Jan 1

### 2.1 NetSuite Integration (CRITICAL PRIORITY - Week 4)

#### NetSuite Research & Design
- [ ] Review NetSuite's new backend API documentation
  - Understand API changes from old to new backend
  - Identify authentication method (OAuth 2.0, Token-based)
  - Review rate limits and best practices
- [ ] Analyze existing NetSuite integration code
  - Document current extraction logic
  - Identify data entities being extracted (invoices, customers, subscriptions, etc.)
  - Document transformation logic
- [ ] Design new NetSuite integration architecture
  - API endpoint mappings
  - Authentication flow
  - Error handling and retry strategy
  - Incremental vs full load strategy
  - Data validation logic
- [ ] Create NetSuite integration technical specification
- [ ] Estimate data volume and API call requirements

#### NetSuite Connector Development
- [ ] Set up NetSuite API credentials
  - Create NetSuite integration record
  - Generate API tokens/OAuth credentials
  - Store credentials in Secret Manager
- [ ] Develop NetSuite API client library
  - Authentication module
  - Request/response handling
  - Rate limiting implementation
  - Pagination handling
  - Error handling and logging
- [ ] Implement data extraction logic
  - Customer master data extraction
  - Invoice data extraction
  - Payment data extraction
  - Subscription data extraction
  - Product/service catalog extraction
- [ ] Implement data transformation logic
  - Schema mapping from NetSuite to STG tables
  - Data type conversions
  - Null handling and defaults
  - Data validation rules
- [ ] Create unit tests for NetSuite connector
- [ ] Create integration tests with NetSuite sandbox (if available)

#### NetSuite Staging Tables & Pipeline
- [ ] Design BigQuery staging tables for NetSuite
  - `STG.netsuite_customers`
  - `STG.netsuite_invoices`
  - `STG.netsuite_payments`
  - `STG.netsuite_subscriptions`
  - `STG.netsuite_products`
- [ ] Create DDL scripts for NetSuite staging tables
  - Define schema with proper data types
  - Add partitioning by date (if applicable)
  - Add clustering keys for query optimization
  - Add table descriptions and column comments
- [ ] Deploy NetSuite staging tables to BigQuery
- [ ] Create Luigi task for NetSuite ingestion
  - Task dependencies and scheduling
  - Incremental load logic (based on last_modified_date)
  - Full refresh logic (for historical loads)
  - Data quality checks
  - Success/failure logging
- [ ] Deploy Luigi task to GKE
- [ ] Create Kubernetes CronJob for NetSuite pipeline (or Luigi scheduler config)

#### NetSuite Integration Testing & Validation
- [ ] Test NetSuite API connection
  - Verify authentication works
  - Test API response handling
  - Verify rate limiting works correctly
- [ ] Run initial data extraction test
  - Extract sample data to GCS
  - Verify data completeness
  - Check for data quality issues
- [ ] Load test data into BigQuery staging tables
  - Verify schema compatibility
  - Check for load errors
  - Validate data types
- [ ] Run full historical data load
  - Extract all historical data
  - Monitor for API errors
  - Validate row counts
- [ ] Validate NetSuite data quality
  - Compare row counts with NetSuite (reports or exports)
  - Verify key fields (customer IDs, invoice totals)
  - Check for missing or null critical fields
  - Validate date ranges
- [ ] Test incremental load logic
  - Run pipeline to extract only new/updated records
  - Verify incremental logic works correctly
  - Validate data freshness
- [ ] Monitor NetSuite pipeline for 3-5 days
  - Check for consistent success
  - Monitor execution time
  - Verify no data drift
- [ ] **NetSuite Integration Sign-off by Dec 31, 2025**
  - Document integration details
  - Create NetSuite troubleshooting guide
  - Update runbook with NetSuite procedures

### 2.2 Salesforce (SFDC) Connection - Week 5

#### SFDC Connection Setup (Existing Instance)
- [ ] Obtain SFDC credentials for existing instance
  - Username, password, security token
  - Or OAuth credentials if available
- [ ] Store SFDC credentials in Secret Manager
- [ ] Test SFDC API connectivity
  - Verify authentication
  - Test API limits and quotas
  - Review SFDC API documentation
- [ ] Audit existing SFDC pipeline code
  - Identify SFDC objects being extracted
  - Review extraction logic and transformations
  - Identify any Enterprise-specific filters to update

#### SFDC Staging Tables & Schema
- [ ] Design BigQuery staging tables for SFDC
  - `STG.sfdc_account`
  - `STG.sfdc_opportunity`
  - `STG.sfdc_contact`
  - `STG.sfdc_lead`
  - `STG.sfdc_campaign`
  - `STG.sfdc_campaign_member`
  - `STG.sfdc_opportunity_line_item`
  - `STG.sfdc_user`
  - Additional custom objects as needed
- [ ] Create DDL scripts for SFDC staging tables
  - Define schema matching SFDC object structure
  - Add partitioning by SystemModstamp or CreatedDate
  - Add clustering for frequently queried fields
  - Document field mappings and descriptions
- [ ] Deploy SFDC staging tables to BigQuery

#### SFDC Luigi Pipeline Development
- [ ] Update existing Luigi tasks for SFDC extraction
  - Update GCP project references
  - Update BigQuery dataset references
  - Update GCS staging bucket references
  - Remove Enterprise-specific SOQL filters
  - Update Commercial-specific filters (if applicable)
- [ ] Create Luigi task for each SFDC object
  - Account extraction task
  - Opportunity extraction task
  - Contact extraction task
  - Lead extraction task
  - Campaign extraction task
  - Custom object extraction tasks
- [ ] Implement incremental load logic
  - Use SystemModstamp for incremental extraction
  - Full refresh logic for weekly/monthly loads
- [ ] Add data quality checks to each task
  - Null value checks for critical fields
  - Referential integrity checks (e.g., Opportunity.AccountId exists)
  - Date range validation
- [ ] Configure task dependencies
  - Account before Opportunity (FK relationship)
  - Campaign before Campaign Member
- [ ] Deploy SFDC Luigi tasks to GKE

#### SFDC Testing & Validation
- [ ] Test SFDC API extraction
  - Extract sample records for each object
  - Verify SOQL queries work correctly
  - Check API call limits and batch sizes
- [ ] Load test data into staging tables
  - Verify schema compatibility
  - Check for data type mismatches
  - Validate special characters and encoding
- [ ] Run full historical load for key objects
  - Load all Accounts
  - Load all Opportunities (last 2 years or as needed)
  - Load all Contacts
  - Load all Leads
- [ ] Validate SFDC data quality
  - Compare row counts with SFDC reports
  - Verify key metrics (open opportunities, customer count)
  - Check for missing parent records (orphaned Opportunities)
- [ ] Test incremental load
  - Create/update test records in SFDC
  - Run incremental extraction
  - Verify new/updated records appear in BigQuery
- [ ] Schedule SFDC pipelines
  - Daily incremental loads (early morning)
  - Weekly full refresh (weekends)
- [ ] Monitor SFDC pipeline for 3-5 days
- [ ] Document SFDC integration
  - Object extraction schedule
  - Data refresh logic
  - Troubleshooting guide
- [ ] **Prepare for SFDC cutover to new instance (planned Q1 2026)**
  - Document cutover steps
  - Create connection configuration checklist
  - Plan for schema validation when new instance is ready

### 2.3 Zuora Connection - Week 5

#### Zuora API Setup
- [ ] Obtain Zuora API credentials
  - API username and password
  - Or OAuth credentials
- [ ] Store Zuora credentials in Secret Manager
- [ ] Review Zuora API documentation
  - Authentication method
  - Available endpoints (REST vs SOAP)
  - Rate limits and quotas
- [ ] Test Zuora API connectivity
- [ ] Audit existing Zuora pipeline code
  - Identify data entities being extracted
  - Review extraction and transformation logic

#### Zuora Staging Tables & Schema
- [ ] Design BigQuery staging tables for Zuora
  - `STG.zuora_account`
  - `STG.zuora_subscription`
  - `STG.zuora_invoice`
  - `STG.zuora_payment`
  - `STG.zuora_product`
  - `STG.zuora_rate_plan`
  - Additional objects as needed
- [ ] Create DDL scripts for Zuora staging tables
  - Define schema based on Zuora object structure
  - Add partitioning by date
  - Add clustering for optimization
- [ ] Deploy Zuora staging tables to BigQuery

#### Zuora Luigi Pipeline Development
- [ ] Update existing Luigi tasks for Zuora extraction
  - Update GCP project and dataset references
  - Update credential references to Secret Manager
  - Remove Enterprise-specific filters
- [ ] Create Luigi task for each Zuora object
  - Account extraction
  - Subscription extraction
  - Invoice extraction
  - Payment extraction
  - Product catalog extraction
- [ ] Implement incremental load logic
  - Use UpdatedDate for incremental loads
  - Implement full refresh for historical data
- [ ] Add data quality checks
  - Subscription status validation
  - Invoice total calculation checks
  - Payment reconciliation checks
- [ ] Deploy Zuora Luigi tasks to GKE

#### Zuora Testing & Validation
- [ ] Test Zuora API extraction
  - Extract sample data for each object
  - Verify API response parsing
- [ ] Load test data into staging tables
  - Verify schema compatibility
  - Check for data issues
- [ ] Run full historical data load
  - Load all active subscriptions
  - Load invoices (last 2 years or as needed)
  - Load payment history
- [ ] Validate Zuora data quality
  - Compare row counts with Zuora reports
  - Verify subscription metrics
  - Validate invoice totals
- [ ] Test incremental load
  - Update test records in Zuora
  - Run incremental extraction
  - Verify updates appear in BigQuery
- [ ] Schedule Zuora pipelines
  - Daily incremental loads
  - Weekly full refresh
- [ ] Monitor Zuora pipeline for 3-5 days
- [ ] Document Zuora integration

### 2.4 AWS S3 Data Sources - Week 6

#### AWS S3 Access Configuration
- [ ] Obtain AWS S3 access credentials
  - AWS Access Key ID and Secret Access Key
  - Or IAM role for cross-account access
- [ ] Identify S3 bucket locations
  - CM usage data bucket (name, region, path)
  - Emma usage data bucket (name, region, path)
- [ ] Store AWS credentials in Secret Manager
- [ ] Test S3 bucket access
  - List files in buckets
  - Download sample files
  - Verify file formats (CSV, JSON, Parquet, etc.)
- [ ] Audit existing S3 pipeline code
  - Review file processing logic
  - Identify file naming patterns and schedules

#### S3 Staging Tables & Schema
- [ ] Analyze S3 file schemas
  - CM usage data file structure
  - Emma usage data file structure
  - Identify schema variations over time
- [ ] Design BigQuery staging tables for S3 data
  - `STG.s3_cm_usage_data`
  - `STG.s3_emma_usage_data`
- [ ] Create DDL scripts for S3 staging tables
  - Define schema to accommodate file structure
  - Add partitioning by usage date
  - Add clustering for query optimization
  - Handle schema evolution (add _raw JSON column if needed)
- [ ] Deploy S3 staging tables to BigQuery

#### S3 Luigi Pipeline Development
- [ ] Update existing Luigi tasks for S3 ingestion
  - Update GCP project and bucket references
  - Update AWS credential references
  - Update file processing logic
- [ ] Create Luigi task for CM usage data
  - List new files in S3 bucket
  - Download files to GCS staging bucket
  - Parse and validate file contents
  - Load data into BigQuery staging table
  - Archive processed files
- [ ] Create Luigi task for Emma usage data
  - Similar logic as CM usage data
- [ ] Implement file tracking mechanism
  - Track processed files to avoid duplicates
  - Store file manifest in BigQuery or GCS
- [ ] Add data quality checks
  - File completeness validation
  - Row count validation
  - Date range validation
- [ ] Configure error handling
  - Handle missing files gracefully
  - Retry logic for transient S3 errors
  - Alert on critical failures
- [ ] Deploy S3 Luigi tasks to GKE

#### S3 Testing & Validation
- [ ] Test S3 file download
  - Download sample files from both buckets
  - Verify file integrity
- [ ] Test file parsing logic
  - Parse sample files
  - Verify schema compatibility
  - Handle edge cases (empty files, malformed data)
- [ ] Load test data into staging tables
  - Verify data loads correctly
  - Check for parsing errors
- [ ] Run full historical data load
  - Process all available historical files
  - Monitor for errors and performance
- [ ] Validate S3 data quality
  - Compare row counts with file record counts
  - Verify usage metrics
  - Check for data gaps or missing files
- [ ] Test incremental load
  - Simulate new files arriving in S3
  - Run incremental extraction
  - Verify only new files are processed
- [ ] Schedule S3 pipelines
  - Daily or hourly extraction (based on file arrival schedule)
- [ ] Monitor S3 pipeline for 3-5 days
- [ ] Document S3 integration
  - File formats and schemas
  - Processing schedule
  - File archiving strategy

### 2.5 Luigi Orchestration Infrastructure - Week 6

#### Luigi Scheduler Deployment
- [ ] Create Luigi scheduler configuration
  - Configure task registry
  - Set up Luigi database backend (PostgreSQL or file-based)
  - Configure logging
- [ ] Create Kubernetes deployment for Luigi scheduler
  - Define resource limits (CPU, memory)
  - Configure persistent volume for Luigi state
  - Set up health checks
- [ ] Create Kubernetes deployment for Luigi workers
  - Define worker pool size
  - Configure resource limits
  - Set up autoscaling (if applicable)
- [ ] Deploy Luigi to GKE
  - Apply Kubernetes manifests
  - Verify pods are running
  - Check logs for errors
- [ ] Set up Luigi web UI (if applicable)
  - Deploy Luigi visualizer
  - Configure authentication
  - Test web UI access
- [ ] Configure Luigi task scheduling
  - Set up cron schedules for each pipeline
  - Define task priorities
  - Configure task retry policies

#### Luigi Monitoring & Alerting
- [ ] Configure Luigi logging
  - Send logs to Cloud Logging
  - Set up log-based metrics
- [ ] Create monitoring dashboards
  - Task execution dashboard (success/failure rates)
  - Task duration dashboard
  - Data freshness dashboard
- [ ] Set up alerting rules
  - Alert on task failures
  - Alert on long-running tasks
  - Alert on data freshness SLA violations
- [ ] Configure notification channels
  - Email notifications
  - Slack notifications (if applicable)
- [ ] Create Luigi troubleshooting guide
  - Common failure modes
  - How to restart failed tasks
  - How to check task logs

#### Pipeline Dependency Management
- [ ] Document task dependency graph
  - Create visual diagram of task dependencies
  - Identify critical path tasks
- [ ] Configure inter-task dependencies
  - Ensure proper execution order
  - Handle cross-source dependencies
- [ ] Test end-to-end pipeline execution
  - Trigger full pipeline run
  - Verify all tasks execute in correct order
  - Check for timing issues
- [ ] Optimize pipeline schedule
  - Stagger tasks to avoid resource contention
  - Ensure tasks complete before business hours
- [ ] Create pipeline restart procedures
  - How to rerun failed pipelines
  - How to backfill historical data

### 2.6 Staging Layer Validation & Sign-off

#### Comprehensive Staging Validation
- [ ] Verify all staging tables are populating
  - Check row counts for each staging table
  - Verify no tables are empty or stale
- [ ] Validate data freshness
  - Check last update timestamp for each staging table
  - Verify data is refreshing on schedule
- [ ] Run data quality checks
  - Null value checks for critical fields
  - Schema validation (expected columns present)
  - Referential integrity checks (where applicable)
  - Date range validation (no future dates, etc.)
- [ ] Compare staging data with source systems
  - Spot-check row counts
  - Validate key metrics (totals, counts)
  - Verify data consistency
- [ ] Test pipeline failure scenarios
  - Simulate source system unavailability
  - Verify error handling works correctly
  - Test retry logic
  - Verify alerting triggers
- [ ] Test pipeline recovery procedures
  - Restart failed tasks
  - Verify data completeness after recovery
- [ ] Verify logging and monitoring
  - Check that logs are being captured
  - Verify monitoring dashboards are updating
  - Test alerting (trigger test alert)

#### Staging Layer Documentation
- [ ] Create data lineage documentation
  - Document source-to-staging mappings
  - Create data flow diagrams
- [ ] Document staging table schemas
  - Create data dictionary for each staging table
  - Document column descriptions
  - Document data types and constraints
- [ ] Document pipeline schedules
  - Create pipeline schedule matrix
  - Document task dependencies
- [ ] Create staging layer troubleshooting guide
  - Common issues and resolutions
  - How to validate data quality
  - How to investigate data discrepancies
- [ ] Update runbook with staging layer procedures
  - Daily operational checks
  - Incident response procedures
  - Escalation paths

#### Phase 2 Completion Checklist
- [ ] **Validation Checklist:**
  - [ ] NetSuite integration complete and stable
  - [ ] SFDC pipelines running successfully
  - [ ] Zuora pipelines running successfully
  - [ ] S3 ingestion pipelines running successfully
  - [ ] All staging tables populating daily
  - [ ] No critical data quality issues
  - [ ] Luigi scheduler operational
  - [ ] Monitoring and alerting functional
  - [ ] Documentation complete
- [ ] Create Phase 2 completion report
- [ ] **Phase 2 Sign-off:** Ready for analytics layer development

---

## Phase 3: Analytics Layer (ODS & EDW) & Tableau Migration
**Duration:** Weeks 7-11 (Dec 29, 2025 - Jan 30, 2026)
**Objective:** Deploy dbt models, migrate Tableau dashboards
**Note:** This is the BIGGEST LIFT per assessment

### 3.1 dbt Project Setup - Week 7

#### dbt Repository & Configuration
- [ ] Set up dbt project structure in `mg-dw-dbt` repo
  - Create standard dbt folder structure
    - `models/` (staging, intermediate, marts)
    - `tests/`
    - `macros/`
    - `seeds/`
    - `snapshots/`
    - `analyses/`
  - Create `dbt_project.yml` configuration
- [ ] Configure dbt profiles
  - Create `profiles.yml` for new BigQuery project
  - Configure connection parameters
  - Set up development and production targets
- [ ] Test dbt connection to BigQuery
  - Run `dbt debug` to verify connection
  - Test read/write permissions
- [ ] Set up dbt documentation
  - Configure documentation generation
  - Set up docs site deployment (GitHub Pages or dbt Cloud)
- [ ] Configure dbt packages
  - Install `dbt_utils`
  - Install any other required packages
  - Document package dependencies
- [ ] Set up dbt testing framework
  - Configure generic tests
  - Create custom test macros
  - Document testing strategy
- [ ] Configure dbt CI/CD
  - Create `cloudbuild-dbt.yaml` for automated deployment
  - Set up dbt compile and test in CI pipeline
  - Configure automated docs deployment

#### dbt Model Organization Strategy
- [ ] Define dbt model layering strategy
  - **Staging layer:** Raw data from STG tables, minimal transformation
  - **Intermediate layer:** Business logic, joins, aggregations
  - **Marts layer:** Final business-facing models (ODS, EDW)
- [ ] Define naming conventions
  - `stg_<source>_<entity>` for staging models
  - `int_<business_area>_<description>` for intermediate models
  - `fct_<business_process>` for fact tables
  - `dim_<business_entity>` for dimension tables
- [ ] Create dbt sources configuration
  - Define sources for all STG tables
  - Configure source freshness checks
  - Document source descriptions
- [ ] Create folder structure
  - `models/staging/sfdc/`
  - `models/staging/netsuite/`
  - `models/staging/zuora/`
  - `models/staging/s3/`
  - `models/intermediate/commercial/`
  - `models/marts/ods/`
  - `models/marts/edw/`

### 3.2 ODS Layer Development - Week 7-8

#### ODS Model Migration
- [ ] Audit existing ODS models in old repository
  - Identify Commercial-specific ODS models
  - Identify Enterprise-specific ODS models (mark for exclusion)
  - Identify shared ODS models (require modification)
- [ ] Clone relevant ODS models to new repo
  - Copy model SQL files
  - Copy model configurations
  - Copy tests
- [ ] Update ODS models for new environment
  - Update source references (point to new STG tables)
  - Update dataset references (STG, ODS)
  - Remove Enterprise-specific logic
    - Remove Enterprise brand filters
    - Remove Zeta-specific calculations
  - Update Commercial-specific logic
    - Add Commercial brand filters where needed
    - Update business rules for Commercial-only
- [ ] Clean up ODS model dependencies
  - Remove dependencies on Enterprise-specific models
  - Update model dependency graph
  - Ensure proper execution order

#### ODS Table Development
- [ ] Create/update ODS customer dimension
  - `dim_customer_ods`
  - Source from SFDC Account staging
  - Include customer attributes, hierarchy
  - SCD Type 2 logic (if applicable)
- [ ] Create/update ODS product dimension
  - `dim_product_ods`
  - Source from SFDC Product or Zuora Product
  - Include product hierarchy and attributes
- [ ] Create/update ODS date dimension
  - `dim_date_ods`
  - Generate date spine
  - Include fiscal calendar attributes
- [ ] Create/update ODS user dimension
  - `dim_user_ods`
  - Source from SFDC User
  - Include sales territory and role
- [ ] Create/update ODS opportunity fact
  - `fct_opportunity_ods`
  - Source from SFDC Opportunity staging
  - Include opportunity metrics and flags
  - Remove Enterprise opportunities
- [ ] Create/update ODS subscription fact
  - `fct_subscription_ods`
  - Source from Zuora subscription staging
  - Include subscription metrics
- [ ] Create/update ODS usage fact
  - `fct_usage_ods`
  - Source from S3 usage staging (CM and Emma)
  - Aggregate usage metrics
- [ ] Create/update ODS invoice fact
  - `fct_invoice_ods`
  - Source from NetSuite or Zuora invoice staging
  - Include invoice line items
- [ ] Additional ODS models as needed
  - Campaign performance
  - Lead conversion
  - Support tickets (if applicable)

#### ODS Model Configuration & Testing
- [ ] Configure ODS model materialization
  - Set incremental vs table materialization
  - Configure partitioning and clustering
  - Set up incremental logic (unique keys, merge strategy)
- [ ] Add dbt tests to ODS models
  - Unique key tests
  - Not null tests for critical fields
  - Referential integrity tests (foreign keys)
  - Custom business logic tests
- [ ] Configure model documentation
  - Add model descriptions
  - Add column descriptions
  - Document business logic
- [ ] Run ODS models in development
  - Execute `dbt run --models ods`
  - Verify models compile and execute
  - Check for SQL errors
- [ ] Validate ODS data quality
  - Check row counts
  - Spot-check data values
  - Verify transformations are correct
- [ ] Deploy ODS models to production
  - Run dbt in production target
  - Verify models deploy successfully
- [ ] Monitor ODS model execution
  - Check execution time
  - Monitor for failures
  - Optimize slow models

### 3.3 EDW Layer Development - Week 8-10

#### EDW Model Migration & Cleanup (BIGGEST LIFT)
- [ ] Comprehensive audit of existing EDW models
  - List all existing EDW models (fact and dimension tables)
  - Categorize each model:
    - Commercial-only (keep as-is)
    - Enterprise-only (exclude)
    - Mixed Commercial/Enterprise (requires cleanup)
  - Document dependencies for each model
  - Identify downstream consumers (Tableau dashboards)
- [ ] Create EDW migration plan
  - Prioritize models by business criticality
  - Define cleanup approach for each model
  - Estimate effort for each model
  - Create model migration checklist
- [ ] Clone EDW models to new repo
  - Copy all relevant model SQL files
  - Copy model configurations
  - Copy tests and documentation
- [ ] Remove Enterprise-specific EDW models
  - Delete models that serve only Enterprise data
  - Document removed models
  - Update dependency graph
- [ ] Clean up mixed Commercial/Enterprise models (CRITICAL TASK)
  - **For each mixed model:**
    - [ ] Identify Enterprise-specific filters (e.g., `brand = 'Enterprise'`)
    - [ ] Identify Enterprise-specific columns or metrics
    - [ ] Identify Enterprise-specific joins
    - [ ] Remove or modify Enterprise logic
    - [ ] Add Commercial-specific filters if needed
    - [ ] Simplify model structure (remove unnecessary complexity)
    - [ ] Update column descriptions
    - [ ] Test model execution
  - This step will be time-consuming with many models
- [ ] Update source and model references
  - Point to new ODS models
  - Update dataset references (ODS, EDW)
  - Update table aliases and naming

#### EDW Dimension Tables
- [ ] Migrate/update `dim_customer`
  - Source from ODS customer dimension
  - Add EDW-specific business logic
  - Add customer segmentation
  - Add lifetime value calculations
  - Remove Enterprise customers
- [ ] Migrate/update `dim_product`
  - Source from ODS product dimension
  - Add product categorization
  - Add product lifecycle attributes
- [ ] Migrate/update `dim_date`
  - Source from ODS date dimension or generate
  - Include fiscal calendar
  - Include holiday flags
- [ ] Migrate/update `dim_geography`
  - Source from SFDC or other geographic data
  - Include region, territory hierarchy
- [ ] Migrate/update `dim_sales_rep`
  - Source from SFDC User
  - Include sales hierarchy
  - Filter to Commercial sales team
- [ ] Additional dimensions as needed
  - Campaign dimension
  - Product category dimension
  - Customer industry dimension

#### EDW Fact Tables
- [ ] Migrate/update `fct_sales`
  - Source from ODS opportunity fact
  - Aggregate sales metrics by customer, product, date
  - Calculate revenue, bookings, MRR
  - Remove Enterprise brand sales
  - Add Commercial-specific metrics
- [ ] Migrate/update `fct_subscription`
  - Source from ODS subscription fact
  - Calculate subscription metrics (ARR, churn, expansion)
  - Filter to Commercial subscriptions
- [ ] Migrate/update `fct_usage`
  - Source from ODS usage fact
  - Aggregate usage by customer, product, date
  - Calculate usage-based metrics
  - Filter to Commercial usage
- [ ] Migrate/update `fct_invoice`
  - Source from ODS invoice fact
  - Calculate billing metrics (billed revenue, collections)
  - Filter to Commercial invoices
- [ ] Migrate/update `fct_pipeline`
  - Source from SFDC Opportunity
  - Calculate pipeline metrics (weighted pipeline, forecast)
  - Filter to Commercial opportunities
- [ ] Migrate/update `fct_campaign_performance`
  - Source from SFDC Campaign and Campaign Member
  - Calculate marketing metrics (leads, conversions, ROI)
  - Filter to Commercial campaigns
- [ ] Additional fact tables as needed
  - Customer health score fact
  - Product adoption fact
  - Support ticket fact (if applicable)

#### EDW Data Marts
- [ ] Create/update Sales Mart
  - Aggregate sales metrics for sales reporting
  - Denormalize for query performance
  - Include top-level KPIs
- [ ] Create/update Marketing Mart
  - Aggregate marketing metrics
  - Include campaign performance, lead conversion
- [ ] Create/update Finance Mart
  - Aggregate financial metrics
  - Include revenue, billings, collections
  - Calculate financial ratios
- [ ] Create/update Customer Success Mart
  - Aggregate customer health metrics
  - Include usage, engagement, satisfaction
- [ ] Create/update Executive Dashboard Mart
  - Top-level KPIs for executive reporting
  - High-level aggregations
  - Pre-calculated metrics for dashboard performance

#### EDW Model Configuration & Optimization
- [ ] Configure EDW model materialization
  - Set incremental vs table materialization
  - Configure partitioning by date
  - Configure clustering by frequently filtered columns
- [ ] Optimize EDW models for performance
  - Identify slow-running models
  - Optimize SQL queries (remove unnecessary joins, use CTEs)
  - Pre-aggregate where appropriate
  - Add indexes (clustering) for query optimization
- [ ] Configure incremental logic
  - Define unique keys for incremental models
  - Set up merge strategy (delete+insert, merge, append)
  - Add incremental filters (date range)
- [ ] Add comprehensive dbt tests
  - Unique key tests
  - Not null tests for critical fields
  - Referential integrity tests (FK to dimension tables)
  - Custom business logic tests
    - Revenue calculations match expected totals
    - No negative values where not expected
    - Date ranges are valid
  - Data freshness tests
- [ ] Add model documentation
  - Add model-level descriptions (business purpose)
  - Add column-level descriptions
  - Document business logic and calculations
  - Document data lineage

#### EDW Data Validation & Reconciliation
- [ ] Run EDW models in development
  - Execute `dbt run --models edw`
  - Verify all models compile and execute
  - Check for SQL errors
  - Monitor execution time
- [ ] Validate EDW data quality
  - Check row counts for fact tables
  - Verify dimension table completeness
  - Spot-check data values
  - Compare key metrics to source systems
- [ ] Reconcile data between old and new EDW
  - Export key metrics from old EDW (Enterprise + Commercial)
  - Export key metrics from new EDW (Commercial only)
  - Compare Commercial subset to validate accuracy
  - Investigate discrepancies
  - Document reconciliation results
- [ ] Run dbt tests
  - Execute `dbt test --models edw`
  - Verify all tests pass
  - Investigate and fix any test failures
- [ ] Deploy EDW models to production
  - Run dbt in production target
  - Monitor deployment for errors
- [ ] Backfill historical data (if needed)
  - Run full refresh for historical periods
  - Verify historical data accuracy
- [ ] Monitor EDW model execution
  - Check daily execution success
  - Monitor execution time
  - Optimize slow models
  - Set up alerting for failures

### 3.4 Tableau Migration - Week 9-11

#### Tableau Account Setup (when available)
- [ ] Procure Tableau Cloud account for MG Commercial
  - Submit procurement request
  - Await account provisioning (expected early 2026)
- [ ] Configure Tableau Cloud instance
  - Set up admin account
  - Configure authentication (SAML/SSO if applicable)
  - Set up user groups
- [ ] Create Tableau site structure
  - Create projects (Sales, Marketing, Finance, Executive)
  - Configure project permissions
- [ ] Set up Tableau-BigQuery connection
  - Create BigQuery service account for Tableau
  - Grant necessary BigQuery permissions
  - Test connection from Tableau

#### Tableau Dashboard Audit (in existing account)
- [ ] Inventory all existing Tableau dashboards
  - List all workbooks and dashboards
  - Categorize each dashboard:
    - Commercial-only (migrate as-is)
    - Enterprise-only (do not migrate)
    - Mixed Commercial/Enterprise (requires refactoring)
  - Document data sources for each dashboard
  - Document dashboard ownership and usage
- [ ] Prioritize dashboards for migration
  - Identify critical business dashboards
  - Identify high-usage dashboards
  - Identify low-value dashboards (consider archiving)
- [ ] Create Tableau migration plan
  - Define migration approach for each dashboard
  - Estimate effort for each dashboard
  - Create migration schedule
  - Assign priority levels

#### Tableau Dashboard Refactoring (in existing account first)
- [ ] For each Mixed Commercial/Enterprise dashboard:
  - [ ] Clone the dashboard (create new version)
  - [ ] Analyze dashboard structure
    - Identify data sources used
    - Identify Enterprise-specific filters
    - Identify Enterprise-specific calculated fields
    - Identify Enterprise-specific charts/visualizations
  - [ ] Update data source connections
    - Point to new BigQuery project
    - Update dataset references (EDW)
    - Update custom SQL queries
    - Remove Enterprise data source connections
  - [ ] Remove Enterprise-specific elements
    - Delete Enterprise brand filters
    - Delete Enterprise-specific calculated fields
    - Delete Enterprise-specific charts/sheets
    - Remove Enterprise data from combined views
  - [ ] Update calculations
    - Remove Enterprise logic from calculated fields
    - Update totals and aggregations (exclude Enterprise)
    - Update growth rates (based on Commercial-only)
  - [ ] Reconfigure filters and parameters
    - Update default filter values
    - Remove Enterprise options from filter lists
    - Update parameter lists
  - [ ] Update dashboard layout
    - Remove or resize containers after removing Enterprise charts
    - Ensure dashboard is visually balanced
    - Update titles and descriptions
  - [ ] Test dashboard functionality
    - Verify all charts load correctly
    - Test all filters and parameters
    - Verify calculations are correct
    - Test drill-down functionality
    - Verify tooltips and actions
  - [ ] Validate dashboard data
    - Spot-check metric values
    - Compare to source data or old dashboard (Commercial subset)
    - Investigate discrepancies
  - [ ] Document dashboard changes
    - Create change log for each dashboard
    - Document new data sources
    - Document removed elements
  - **Repeat for all mixed dashboards** (this will be time-consuming)

#### Tableau Dashboard Migration (Commercial-only)
- [ ] For each Commercial-only dashboard:
  - [ ] Update data source connections
    - Point to new BigQuery project
    - Update dataset references
  - [ ] Test dashboard functionality
    - Verify all features work
    - Validate data accuracy
  - [ ] Document dashboard

#### Tableau Performance Optimization
- [ ] Optimize dashboard performance
  - Identify slow-loading dashboards
  - Optimize data source queries (use extracts vs live connections)
  - Reduce number of marks (aggregations)
  - Optimize filters (use context filters, cascading filters)
  - Use dashboard actions instead of filters where possible
- [ ] Create Tableau extracts where needed
  - For large datasets, create extracts
  - Schedule extract refreshes
- [ ] Test dashboard performance
  - Measure load times
  - Optimize slow dashboards

#### Tableau Migration to New Account (when available)
- [ ] Export workbooks from existing Tableau account
  - Download .twbx files for all migrated dashboards
  - Document data source configurations
- [ ] Import workbooks to new Tableau Cloud account
  - Upload .twbx files
  - Reconfigure data source connections (should be pointing to new BigQuery project)
  - Test dashboard functionality in new account
- [ ] Migrate data sources
  - Publish shared data sources to new account
  - Configure data source refresh schedules
- [ ] Configure dashboard permissions
  - Set up user access for each dashboard
  - Configure project-level permissions
- [ ] Migrate user favorites and subscriptions
  - Document user favorites from old account
  - Set up subscriptions in new account
- [ ] Update dashboard URLs
  - Communicate new dashboard URLs to users
  - Update bookmarks and links

#### Tableau Documentation & Training
- [ ] Create Tableau dashboard catalog
  - List all dashboards with descriptions
  - Document dashboard owners
  - Document data sources and refresh schedules
- [ ] Create dashboard user guides
  - How to access dashboards
  - How to use filters and parameters
  - How to interpret metrics
- [ ] Update dashboard descriptions in Tableau
  - Add descriptions to each dashboard
  - Add metadata (last updated, data source, owner)
- [ ] Create Tableau troubleshooting guide
  - Common issues and resolutions
  - How to refresh extracts
  - How to report issues
- [ ] Plan user training sessions
  - Schedule training for key user groups
  - Prepare training materials (slides, demos)

### 3.5 User Acceptance Testing (UAT) Preparation - Week 11

#### UAT Planning
- [ ] Define UAT scope
  - Identify key dashboards for UAT
  - Identify key metrics to validate
  - Identify UAT participants (RevOps users, finance users, etc.)
- [ ] Create UAT test plan
  - Define UAT objectives
  - Create test scenarios
  - Define acceptance criteria
- [ ] Create UAT test cases
  - For each key dashboard:
    - Test case 1: Verify dashboard loads
    - Test case 2: Validate key metric X
    - Test case 3: Test filter functionality
    - Test case 4: Compare to old dashboard (Commercial subset)
    - Additional test cases as needed
- [ ] Create UAT data validation checklist
  - Row count comparisons
  - Metric value comparisons (revenue, customer count, etc.)
  - Data freshness checks
  - Historical trend validation

#### UAT Coordination
- [ ] Schedule UAT sessions
  - Coordinate with RevOps and other stakeholders
  - Schedule kickoff meeting
  - Schedule daily/weekly check-ins
  - Schedule wrap-up and sign-off meeting
- [ ] Prepare UAT environment
  - Ensure all dashboards are accessible
  - Ensure data is current
  - Prepare demo environment if needed
- [ ] Prepare UAT documentation
  - Create UAT instructions
  - Create UAT feedback form
  - Create issue log template
- [ ] Communicate UAT plan to stakeholders
  - Send UAT schedule
  - Send UAT instructions
  - Set expectations for UAT duration

### 3.6 Phase 3 Validation & Sign-off

#### Comprehensive Analytics Layer Validation
- [ ] Verify all dbt models deployed successfully
  - Check model execution logs
  - Verify no errors or warnings
- [ ] Verify data quality in EDW
  - Run data quality checks
  - Validate key metrics
  - Ensure data freshness
- [ ] Verify all Tableau dashboards migrated
  - Check dashboard list
  - Verify all dashboards accessible
- [ ] Test end-to-end data flow
  - Staging � ODS � EDW � Tableau
  - Verify data latency is acceptable
  - Test data refresh end-to-end
- [ ] Review dbt documentation
  - Verify docs site is up to date
  - Check model and column descriptions
- [ ] Review Tableau documentation
  - Verify dashboard catalog is complete
  - Check user guides

#### Phase 3 Completion Checklist
- [ ] **Validation Checklist:**
  - [ ] All dbt models deployed and running
  - [ ] ODS layer validated
  - [ ] EDW layer validated
  - [ ] Enterprise-specific models removed
  - [ ] All Tableau dashboards refactored/migrated
  - [ ] Dashboards pointing to new BigQuery project
  - [ ] Data quality checks passing
  - [ ] UAT preparation complete
  - [ ] Documentation complete
- [ ] Create Phase 3 completion report
- [ ] **Phase 3 Sign-off:** Ready for validation and production cutover

---

## Phase 4: Validation, UAT & Production Cutover
**Duration:** Weeks 12-15 (Feb 2 - Feb 27, 2026)
**Objective:** Validate with business users, execute production cutover, stabilize

### 4.1 Internal Validation - Week 12

#### Comprehensive System Validation
- [ ] Run full end-to-end data pipeline
  - Trigger all staging pipelines
  - Run all dbt models
  - Refresh all Tableau extracts
  - Monitor for failures
- [ ] Data quality validation
  - Run comprehensive data quality checks
  - Validate data across all layers (STG, ODS, EDW)
  - Compare key metrics to old system (Commercial subset)
  - Investigate and resolve discrepancies
- [ ] Data freshness validation
  - Verify staging tables are updating on schedule
  - Verify ODS/EDW models are running on schedule
  - Check data latency (source system � Tableau)
- [ ] Pipeline reliability testing
  - Monitor pipeline success rates for 1 week
  - Test failure scenarios
    - Simulate source system unavailability
    - Simulate data quality issues
    - Verify error handling and alerting
  - Test recovery procedures
    - Restart failed pipelines
    - Backfill missing data
- [ ] Performance testing
  - Measure pipeline execution times
  - Measure Tableau dashboard load times
  - Identify performance bottlenecks
  - Optimize slow components

#### Monitoring & Alerting Validation
- [ ] Verify monitoring dashboards
  - Check all monitoring dashboards are working
  - Verify metrics are updating
- [ ] Test alerting
  - Trigger test alerts for each alert rule
  - Verify alerts are received
  - Verify alert content is clear and actionable
- [ ] Review alert thresholds
  - Adjust alert thresholds based on observed behavior
  - Reduce false positive alerts
- [ ] Test incident response procedures
  - Simulate a production incident
  - Follow incident response runbook
  - Verify escalation procedures work

### 4.2 User Acceptance Testing (UAT) - Week 12-13

#### UAT Kickoff
- [ ] Conduct UAT kickoff meeting
  - Review UAT objectives and scope
  - Walk through UAT test plan
  - Demonstrate new dashboards
  - Address questions
- [ ] Provide UAT access
  - Grant access to new Tableau dashboards
  - Provide UAT instructions and test cases
  - Provide feedback form and issue log

#### UAT Execution
- [ ] Facilitate UAT sessions
  - Walk through key dashboards with users
  - Guide users through test cases
  - Answer questions
- [ ] Collect UAT feedback
  - Monitor feedback form submissions
  - Log issues in issue tracker
  - Prioritize feedback (critical, high, medium, low)
- [ ] Daily UAT check-ins
  - Review feedback received
  - Discuss issues found
  - Provide status updates
- [ ] For each key dashboard, validate with users:
  - [ ] Sales dashboards (with sales ops/leadership)
    - Validate revenue metrics
    - Validate pipeline metrics
    - Validate forecasts
  - [ ] Marketing dashboards (with marketing ops)
    - Validate campaign performance
    - Validate lead metrics
    - Validate conversion rates
  - [ ] Finance dashboards (with finance team)
    - Validate billing metrics
    - Validate collections
    - Validate revenue recognition
  - [ ] Customer success dashboards (with CS ops)
    - Validate usage metrics
    - Validate health scores
    - Validate churn metrics
  - [ ] Executive dashboards (with leadership)
    - Validate top-level KPIs
    - Validate trends and growth rates

#### UAT Issue Resolution
- [ ] Triage UAT issues
  - Categorize issues (data, dashboard, performance, training)
  - Prioritize issues (critical, high, medium, low)
  - Assign issues for resolution
- [ ] Resolve critical issues
  - Data discrepancies (investigate root cause, fix pipelines/models)
  - Dashboard errors (fix Tableau workbooks)
  - Performance issues (optimize queries/dashboards)
- [ ] Resolve high-priority issues
  - Missing data or features
  - Incorrect calculations
  - Usability issues
- [ ] Decide on medium/low-priority issues
  - Fix now vs defer to post-launch
  - Document deferred items for backlog
- [ ] Retest after fixes
  - Verify issues are resolved
  - Obtain user confirmation
- [ ] Track UAT progress
  - Monitor test case completion
  - Track issue resolution
  - Identify blockers

#### UAT Sign-off
- [ ] Obtain UAT sign-off from stakeholders
  - Sales ops sign-off
  - Marketing ops sign-off
  - Finance sign-off
  - Customer success ops sign-off
  - Executive sponsor sign-off
- [ ] Document UAT results
  - Summary of testing performed
  - Issues found and resolved
  - Outstanding issues and mitigation plan
  - User feedback and satisfaction
- [ ] Create UAT completion report

### 4.3 Production Cutover Planning - Week 13-14

#### Cutover Strategy Definition
- [ ] Define cutover approach
  - Big bang cutover (switch all at once) vs gradual cutover
  - Recommendation: Gradual cutover with parallel operation
    - Run both old and new systems for 1 month
    - Users access new dashboards, but old dashboards available as reference
    - Monitor for issues before full cutover
- [ ] Define cutover criteria
  - All UAT issues resolved or mitigated
  - All critical dashboards validated
  - Monitoring and alerting operational
  - Runbooks and documentation complete
  - Support team trained
- [ ] Define cutover date
  - Target cutover date (late February)
  - Communicate cutover date to stakeholders
- [ ] Create cutover checklist
  - Pre-cutover tasks
  - Cutover execution tasks
  - Post-cutover tasks
  - Rollback tasks (if needed)

#### Cutover Preparation
- [ ] Create rollback plan
  - Define rollback triggers (what would cause rollback)
  - Document rollback procedures
  - Prepare rollback scripts
  - Test rollback procedures
- [ ] Prepare cutover communication
  - Draft cutover announcement email
  - Create cutover FAQ
  - Prepare user communication plan
- [ ] Schedule cutover window
  - Choose low-impact time (weekend or off-hours)
  - Reserve time for cutover execution
  - Schedule cutover team availability
- [ ] Prepare cutover runbook
  - Step-by-step cutover procedures
  - Contact information for support
  - Troubleshooting guide
- [ ] Final pre-cutover validation
  - Run full data pipeline
  - Validate all dashboards
  - Verify monitoring and alerting
  - Review open issues

### 4.4 Production Cutover Execution - Week 14

#### Pre-Cutover Tasks
- [ ] Communicate cutover to all stakeholders
  - Send cutover announcement
  - Remind users of new dashboard URLs
  - Set expectations for support
- [ ] Final data validation
  - Run data quality checks
  - Verify data freshness
  - Validate key metrics
- [ ] Freeze changes
  - No code deployments during cutover window
  - Lock down production access
- [ ] Backup old system state
  - Document current state of old system
  - Create data snapshots (if needed)

#### Cutover Execution
- [ ] Execute cutover steps
  - [ ] Mark new system as production
  - [ ] Update Tableau dashboard links (if needed)
  - [ ] Enable production monitoring and alerting
  - [ ] Disable old system pipelines (optional, or keep running in parallel)
- [ ] Verify cutover success
  - [ ] All pipelines running successfully in new system
  - [ ] All dashboards accessible
  - [ ] Monitoring and alerting functioning
  - [ ] No critical errors
- [ ] Communicate cutover completion
  - Send cutover completion announcement
  - Provide support contact information
  - Remind users of training resources

#### Post-Cutover Monitoring (First 24 Hours)
- [ ] Monitor system closely
  - Watch for pipeline failures
  - Watch for data quality issues
  - Watch for dashboard issues
  - Watch for user-reported issues
- [ ] Monitor alerting
  - Respond to any alerts immediately
  - Escalate critical issues
- [ ] Provide rapid support
  - Be available for user questions
  - Resolve issues quickly
  - Communicate status to stakeholders
- [ ] Log all issues
  - Track issues in issue tracker
  - Categorize and prioritize issues
  - Document resolutions

### 4.5 Stabilization & Monitoring - Week 14-15

#### First Week Post-Cutover
- [ ] Daily system health checks
  - Verify all pipelines ran successfully
  - Check data freshness
  - Review monitoring dashboards
  - Review user-reported issues
- [ ] Daily standup meetings (or check-ins)
  - Review previous day's issues
  - Discuss open issues
  - Plan for day ahead
- [ ] Rapid issue resolution
  - Triage and resolve issues quickly
  - Communicate resolutions to stakeholders
- [ ] User support
  - Respond to user questions
  - Provide additional training if needed
  - Collect user feedback
- [ ] Refine monitoring and alerting
  - Adjust alert thresholds based on real usage
  - Add new alerts if gaps identified
  - Reduce noisy alerts

#### First Month Post-Cutover (Weeks 14-15+)
- [ ] Weekly system health checks
  - Review pipeline success rates
  - Review data quality metrics
  - Review dashboard usage
  - Review user-reported issues
- [ ] Weekly check-in meetings
  - Review previous week
  - Discuss trends and patterns
  - Plan improvements
- [ ] Optimize system performance
  - Identify and optimize slow pipelines
  - Optimize slow Tableau dashboards
  - Tune BigQuery queries
- [ ] Continuous improvement
  - Implement enhancements based on user feedback
  - Add new features or dashboards
  - Improve data quality checks
- [ ] Monitor costs
  - Review GCP costs
  - Identify cost optimization opportunities
  - Adjust resource allocation

### 4.6 Zeta Cleanup Coordination - Week 15

#### Zeta Cleanup Planning
- [ ] Coordinate with Zeta team
  - Schedule cleanup planning meeting
  - Review cleanup scope and timeline
  - Define cleanup responsibilities
- [ ] Provide Zeta with asset lists
  - List of Commercial-specific pipelines to disable
  - List of Commercial-specific dbt models to archive
  - List of Commercial-specific Tableau dashboards to archive
  - List of Commercial-specific GCS files to delete
- [ ] Create cleanup runbook for Zeta
  - Step-by-step procedures for disabling/archiving assets
  - Verification steps
  - Rollback procedures

#### Zeta Cleanup Execution (coordinated)
- [ ] Zeta disables Commercial pipelines in their GCP project
  - Verify pipelines are disabled
  - Verify no impact to Enterprise pipelines
- [ ] Zeta archives Commercial dbt models
  - Move models to archive folder or delete
  - Verify no dependencies broken
- [ ] Zeta archives Commercial Tableau dashboards
  - Move dashboards to archive project or delete
  - Verify no user access issues
- [ ] Verify cleanup completion
  - Review Zeta's cleaned-up environment
  - Ensure separation is complete
- [ ] Document cleanup completion

### 4.7 Documentation & Knowledge Transfer - Week 15

#### Final Documentation Review
- [ ] Review and update all documentation
  - Architecture documentation
  - Runbooks
  - Data source documentation
  - Pipeline documentation
  - dbt model documentation
  - Tableau dashboard documentation
- [ ] Ensure documentation completeness
  - All systems documented
  - All procedures documented
  - All troubleshooting guides complete
- [ ] Publish documentation
  - Make documentation accessible to team
  - Share with stakeholders
- [ ] Create documentation index
  - Table of contents for all documentation
  - Quick reference guide

#### Knowledge Transfer
- [ ] Conduct knowledge transfer sessions
  - Session 1: Infrastructure overview (GCP, GKE, etc.)
  - Session 2: Data pipelines and Luigi
  - Session 3: dbt models and EDW
  - Session 4: Tableau dashboards
  - Session 5: Monitoring and incident response
  - Session 6: Troubleshooting and support
- [ ] Record knowledge transfer sessions
  - Create video recordings for future reference
- [ ] Create FAQ document
  - Common questions and answers
  - Based on UAT feedback and support questions
- [ ] Define ongoing support model
  - Who provides support (you, team, vendor?)
  - Support escalation procedures
  - SLAs for issue resolution
- [ ] Transition to BAU (business as usual)
  - Define ongoing operational responsibilities
  - Define continuous improvement process
  - Define change management process

### 4.8 Project Closeout - Week 15

#### Project Completion Checklist
- [ ] **Final Validation Checklist:**
  - [ ] All systems operational and stable
  - [ ] UAT completed and signed off
  - [ ] Production cutover successful
  - [ ] 1 month of stable dual operation (target: 1 month post-cutover)
  - [ ] All critical issues resolved
  - [ ] Documentation complete
  - [ ] Knowledge transfer complete
  - [ ] Zeta cleanup coordinated
  - [ ] Costs within budget
- [ ] Create final project status report
  - Executive summary
  - Project objectives achieved
  - Key metrics (timeline, budget, quality)
  - Lessons learned
  - Outstanding items and next steps
- [ ] Conduct lessons learned session
  - What went well
  - What could be improved
  - Recommendations for future projects
- [ ] Archive project artifacts
  - Project plans and schedules
  - Meeting notes
  - Issue logs
  - Test results
  - Communication
- [ ] Celebrate success!
  - Recognize team contributions
  - Thank stakeholders
  - Celebrate project completion

---

## Phase 5: Buffer & Ongoing Stabilization
**Duration:** Weeks 16-20 (Mar 2 - Mar 31, 2026)
**Objective:** Buffer for unforeseen issues, continued stabilization, ongoing support

### 5.1 Distributed Buffer Throughout Project

**Note:** Buffer time should be distributed throughout the project, not just at the end. However, keeping final buffer for major unforeseen issues.

#### Potential Uses of Buffer Time
- [ ] Extended UAT cycles (if users need more time)
- [ ] Addressing unforeseen technical challenges
- [ ] Handling external dependency delays (SFDC new instance, Tableau Cloud account, etc.)
- [ ] Additional data validation and reconciliation
- [ ] Performance optimization
- [ ] Additional user training
- [ ] Handling holiday disruptions
- [ ] Resolving complex data discrepancies
- [ ] Adding features or dashboards requested during UAT
- [ ] Extended parallel operation period

### 5.2 Success Criteria: 1 Month of Stable Dual Operation

**Target:** Both warehouses (old Enterprise+Commercial, new Commercial-only) running independently for 1 month without critical issues.

#### Stability Monitoring
- [ ] Track pipeline success rates
  - Target: >99% success rate
  - Track failures and root causes
- [ ] Monitor data freshness
  - Ensure data refreshes on schedule
  - Track SLA compliance
- [ ] Monitor data quality
  - Track data quality check results
  - Investigate anomalies
- [ ] Monitor dashboard usage
  - Track user adoption of new dashboards
  - Identify unused dashboards
- [ ] Track user-reported issues
  - Categorize and track issues
  - Measure issue resolution time
  - Track issue trends

#### Validation Gates
- [ ] Week 1 post-cutover: No critical incidents
- [ ] Week 2 post-cutover: Pipeline success rate >95%
- [ ] Week 3 post-cutover: User satisfaction score >4.0/5.0
- [ ] Week 4 post-cutover: No unresolved critical or high-priority issues
- [ ] **1 month post-cutover: Project success criteria met**

### 5.3 Ongoing Operations & Continuous Improvement

#### Transition to BAU Operations
- [ ] Define BAU operational responsibilities
  - Daily monitoring and checks
  - Weekly data quality reviews
  - Monthly performance reviews
- [ ] Define incident management process
  - Incident detection and logging
  - Incident triage and prioritization
  - Incident resolution and communication
  - Post-incident review
- [ ] Define change management process
  - Request new features or changes
  - Assess impact and effort
  - Schedule and implement changes
  - Communicate changes to users

#### Continuous Improvement Backlog
- [ ] Create backlog of improvement items
  - Deferred UAT issues (medium/low priority)
  - User-requested enhancements
  - Performance optimizations
  - New dashboards or reports
  - Data quality improvements
- [ ] Prioritize backlog
  - Business value vs effort
  - Quick wins vs strategic initiatives
- [ ] Plan implementation
  - Schedule improvements into regular cadence
  - Communicate plans to stakeholders

#### Post-Project Review
- [ ] Conduct post-project review (after 1-2 months)
  - Review project outcomes against objectives
  - Assess user satisfaction
  - Assess system stability and performance
  - Review costs vs budget
  - Identify areas for improvement
- [ ] Document lessons learned
  - What worked well
  - What could be improved
  - Recommendations for future
- [ ] Share lessons learned with organization

---

## Summary: Critical Path & Milestones

### Critical Path Items
1. **NetSuite Integration (Week 4)** - MUST complete by Dec 31, 2025
2. **EDW Model Cleanup (Weeks 8-10)** - Biggest lift, many models to clean
3. **Tableau Refactoring (Weeks 9-11)** - Time-consuming, many dashboards
4. **UAT (Weeks 12-13)** - Requires user availability, may extend

### Key Milestones
- **Week 3:** Phase 1 complete - Infrastructure ready
- **Week 6:** Phase 2 complete - All data sources connected, NetSuite complete by Dec 31
- **Week 11:** Phase 3 complete - dbt models and Tableau dashboards migrated, ready for UAT
- **Week 13:** UAT complete - User sign-off obtained
- **Week 14:** Production cutover - New system live
- **Week 18+:** 1 month of stable operation - Project success

### Risk Mitigation
- **Single engineer constraint:** Sequential execution, no parallel work
- **Holiday disruptions:** PTO on Dec 24, 25, 31, Jan 1 - plan around holidays
- **NetSuite critical path:** Prioritize in Week 4, complete before year-end
- **EDW cleanup effort:** Allocate sufficient time (3 weeks), may need buffer
- **Tableau refactoring:** Allocate sufficient time (3 weeks), iterative approach
- **UAT delays:** Build buffer into timeline, account for user PTO
- **External dependencies:** SFDC and Tableau new accounts may delay, plan for cutover

### Weekly Effort Estimation (15 hours/week)
- **Phase 1 (3 weeks):** 45 hours - Infrastructure setup
- **Phase 2 (3 weeks):** 45 hours - Data source connections (NetSuite is priority)
- **Phase 3 (5 weeks):** 75 hours - dbt and Tableau (biggest lift)
- **Phase 4 (4 weeks):** 60 hours - UAT, cutover, stabilization
- **Phase 5 (5 weeks):** 75 hours - Buffer and ongoing stabilization
- **Total:** 300 hours over 20 weeks

---

## Next Steps

1. **Review this task breakdown** - Validate completeness and accuracy
2. **Refine estimates** - Adjust task durations based on your expertise
3. **Create weekly schedule** - Break down into week-by-week plan
4. **Set up project tracking** - Use GitHub Projects, Jira, or similar
5. **Communicate plan** - Share with stakeholders for alignment
6. **Begin execution** - Start Phase 1 tasks

---

**Document Version:** 1.0
**Created:** [Date]
**Author:** Senior Architect (Claude Code)
