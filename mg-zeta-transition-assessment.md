## Questions

---

Resource & Capacity Planning

  1. How many engineers are dedicated to this project, and what is their allocation percentage?
  (e.g., 2 FTE, 3 part-time at 50%)
    - This directly impacts whether phases can run in parallel or must be sequential
  2. Are the team members working on this project also committed to other initiatives?
    - Critical for understanding realistic velocity and potential context-switching overhead
  3. Which team members own which deliverables, and are there any single points of failure?
    - Helps identify resource bottlenecks and where parallel work is feasible

  Critical Path & Dependencies

  4. What is the actual status of the critical external dependencies right now?
    - New Salesforce instance: provisioned? schema confirmed?
    - NetSuite new integration: started? completion date?
    - AWS S3 structure: confirmed stable by Zeta?
    - Tableau Cloud procurement: approved? timeline?
  5. What are the hard-stop dependencies that prevent starting later phases early?
    - Can any Phase 2 work (data connections) start during Phase 1?
    - Can Phase 3 (analytics layer) be partially developed before Phase 2 completes?
  6. What specific coordination points require Zeta team involvement, and what are their capacity 
  constraints?
    - AWS S3 bucket setup timeline
    - Testing of separated data flows
    - GCP account transfer process

  Phase-Specific Deep Dive

  7. Phase 1 (Foundation - 3 weeks): Is this sufficient given the following activities?
    - GCP project setup + all service deployments
    - GitHub repository migration
    - CI/CD pipeline setup
    - IAM and Secret Manager configuration
    - What's the typical timeline for GCP organizational setup and approvals?
  8. Phase 2 (Staging Layer - 3 weeks): How many data source connections and Luigi pipelines are we
   talking about?
    - Exact number of pipelines to redeploy
    - Complexity of each data source integration
    - Historical data volume to backfill (if any)
  9. Phase 3 (Analytics Layer - 5 weeks): Why does this take 5 weeks vs staging's 3 weeks?
    - How many dbt models need deployment?
    - How many Tableau dashboards need migration?
    - What is the "Incremental and In-Parallel" validation strategy, and how long should it run?
  10. Phase 4 (Validation - 4 weeks): What are the specific gates and acceptance criteria?
    - How long for UAT with business stakeholders?
    - What's the production cutover strategy (big bang vs gradual)?
    - How much time for monitoring post-cutover before declaring success?

  Risk & Contingency

  11. What historical data from previous migrations informed these timeline estimates?
    - Has the team done similar GCP migrations before?
    - What were the actual vs estimated timelines on comparable projects?
  12. The 80% completion by end of January target falls in Week 10-11, but Phase 3 doesn't end 
  until Week 11. Is this realistic?
    - Which specific deliverables constitute the 80%?
    - Does this assume Phase 1-2 complete on time with no delays?
  13. What specific risks justify the 4-week buffer in Phase 5, and are there phase-specific 
  buffers needed?
    - Should buffer time be distributed across phases rather than at the end?
    - What happens if NetSuite integration (critical by end of 2025) is delayed?

  Technical Complexity

  14. What is the complexity of breaking down Tableau dashboards that have combined 
  Enterprise/Commercial views?
    - How many dashboards have mixed dependencies?
    - What's the effort to refactor vs rebuild?
  15. How will you handle the NetSuite backend upgrade integration that must be completed by 
  year-end?
    - This appears in Week 4-6 but is marked as critical by end of 2025 (Week 6 is Dec 26)
    - Is there contingency if this integration takes longer?
  16. What data reconciliation and validation processes need to happen, and how much time do they 
  require?
    - Staging layer validation
    - Analytics layer validation
    - Business metric validation
    - Historical data accuracy checks

  Assumptions & Constraints

  17. Have Zeta's outstanding decisions been resolved?
    - Billing system approach (impacts data models)
    - CD usage data export mechanism (AWS vs GCS)
    - Timeline for these decisions
  18. What holidays or blackout periods fall within the project timeline that might affect 
  productivity?
    - Thanksgiving (late Nov), Christmas/New Year (late Dec), other org-specific blackouts
    - Phase 2 ends Dec 26 - is the team working during holidays?

  Success Metrics & Milestones

  19. What are the specific weekly milestones to track "adherence to timelines" as mentioned in 
  success criteria?
    - What deliverables define phase completion?
    - What triggers a red/yellow/green status?
  20. What constitutes a successful "production cutover" that allows the project to close?
    - X days of stable pipeline runs?
    - Business stakeholder sign-off?
    - Zero critical incidents for Y days?

## Answers

---

1. I am the engineer in charge of the project, no one else. I can dedicate 15 hours a week. 

2. Just me. 

3. I own the entire project. 

4. New SFDC instance should be up and running by the beginning of 2026, this is just an estimate though. The NetSuite new integration is my responsability and that needs to be done before the end of the year. I'm not worrying about the AWS for Zeta, this is not a priority right now, keep it as a latent risk. New Tableau instance should be available by the beginning of 2026 as well. 

5. Well, I am the bottleneck. Because it is only me, I will first work on foundational provisioning and the stage ayer. I would prefer to complete staging completely before moving on to analytical layer. 

6. For Zeta, the main effort here is clean up. Since they are inheriting the existing project, we need to remove the parts that are related to Commercial (MG). This involves, disabling pipelines, deleting/archiving dbt models, archiving tableau dashboards. The end goal is that the GCP project that they are inherinting is cleaned up and only relevant to Zeta (formerly MG Enterprise Brands). 

7. Let's focus right now on activities and not timelines. These activities are 'aggregated buckets' of more granular activities. e.g. 'GCP Project Setup' can be broken down in the following way: 
+ Creation of new GCP Project (DONE)
+ Deployment of GKE cluster infra
+ Deployment of Bigquery datasets
+ Deployment od GCS buckets
... 
So on and so forth for all other tasks. There are some of them that will be blockers to other tasks. e.g. I can't create Cloud Build triggers before the new GH repos are available. 

8. Here's the list:
+ SFDC (will connect to existing instance until new one becomes available)
+ Netsuite (requires to rebuild integration)
+ Zuora
+ S3 buckets for usage data (CM and Emma)

9. This is the largest effort because here is where we port over the old dbt models to the new repo, we clean up everything that was related to "MG-Enterprise" (now Zeta) and once we have the cleaned up dbt models, we have to do the same clean up on the Tableau repo (will begin in the old account, move over to new account once available) and then reconnect the Tableau notebooks to the new dbt models. This in my opinion is the biggest lift. 

10. Here I will work with RevOps users to validate the new Tableau dashboards and have them confirm that the numbers match the privious version of the reports. I will also do basic validation on my end e.g. row counts, etc. 

11. This is empirical estimation. I prefer to over-estimate how long it will take and then have the surprise that it took less than expected than the other way around. Plus, I am the person who designed and built the existing architecture, so I am highly familiar with it. 

12. I exoect all of the building and migration activities to be completed by end of Jan. Maybe that's not really 80% but what I really want is building job is done by then. So that we have Feb and March for validation and debugging. 

13. Yes, this should really be spread out accross all other phases and not be pushed towards the end. I still like to have a little buffer at the end for safety.  Netsuite new integration can't be delayed until next year, otherwise this will cause disruption in the existing reporting and that is not acceptable. 

14. A lot of dashboards have combined Commercial/Enterprise logic. I don't have the actual number. 

15. That will probably be my first task when we get to Stage Layer phase. I expect that integration should take me about a week work. 

16. Staging does not require much validation because I will essentially be using the same pipelines and the same sources to populate STG. The biggest validation come in EDW models in DBT and Tableau reports. 

17. TBD

18. I don't have lots of PTO planned. Only 24 and 25 and 31 and 1. However, other people might have PTO so this is why I want to get the building items crossed out quickly so that we have enough time to validate while accounting for the fact that some people might go on PTO and UAT gets delayed cus of it. 

19. We should have deliverables defined on a weekly basis based on our task definitions for each week. This is exactly why we are going over this excersice. 

20. 1 month of running both data warehouse independently without missing assets or unexpected error. 

