# Data Pipeline and Data Product Verification

## Data Pipeline Verification [ml-analytic-service]

### Step 1: Verify Deployment
1. Ensure the build and deployment job for `ml-analytic-service` is completed.
2. Log into the server of `ml-analytic-service`.
3. Check the logs.
4. If the log states **Worker ready**, then the scripts are running successfully.

### Step 2: Set Up Druid
1. Navigate to the Jenkins job path:
   - `Dashboard/Deploy/{ENV-NAME}/DataPipeline/DruidIngestion`
2. Click on **Build with Parameters**.
3. Fill in the required details:
   - **Private_Branch**
   - **branch_or_tag**
4. Choose the ingestion task name from the list. If not available, add the following task names:
   ```
   raw_sl_survey, raw_sl_survey_meta, raw_sl_survey_status_completed, raw_sl_survey_status_inprogress,
   raw_sl_survey_status_started, raw_sl_observation, raw_sl_observation_meta, raw_sl_observation_status_completed,
   raw_sl_observation_status_inprogress, raw_sl_observation_status_started
   ```
5. Click **Build**.
6. Once the build is completed:
   - Go to the **Druid UI**.
   - Click on **Supervisors** and ensure all 10 supervisors are in **RUNNING** state with the following data sources:
     ```
     sl-survey, sl-survey-meta, sl-survey-status-completed, sl-survey-status-inprogress, sl-survey-status-started,
     sl-observation, sl-observation-meta, sl-observation-status-completed, sl-observation-status-inprogress,
     sl-observation-status-started
     ```
   - Check the **Tasks** section to confirm all data source tasks are in **RUNNING** state.

### Step 3: Validate Submission
1. Submit **survey** and **observation** via the portal.
2. Check the pod logs for **submission IDs**.

### Step 4: Verify Data Sources
1. Confirm that all the mentioned data sources are created.
2. Ensure data is present after submission.

---

## Data Product Verification [sunbird-core-dataproducts]

### Step 1: Build and Deploy
1. Build and deploy `sunbird-core-dataproducts` pointing to the latest **branch** or **tag**.

### Step 2: Configure MongoDB
1. Login to the **MongoDB server**.
2. Use the database:
   ```
   use ml-survey
   ```

#### Create Collection for Data Product Configurations
```
db.createCollection('dataProductConfigurations')
```

#### Insert Configuration Documents

**Survey Status Report:**
```json
{
   "report": "surveyStatusReport",
   "config": {
      "reportColumns": { "createdBy": "UUID", "completedDate": "Submission Date", "createdAt": "Survey Started Date", "organisationName": "Organisation Name", "solutionExternalId": "Solution External Id", "solutionId": "Solution Id", "solutionName": "Solution Name", "surveyName": "Survey Name", "surveyId": "Survey Id", "surveySubmissionId": "Survey Submission Id", "isAPrivateProgram": "Private Program" },
      "sortingColumns": "UUID,Organisation Name,Solution External Id,Solution Id,Solution Name,Survey Id,Survey Name,Survey Submission Id,Private Program,Survey Started Date,Submission Date"
   }
}
```

**Survey Question Report:**
```json
{
   "report": "surveyQuestionReport",
   "config": {
      "reportColumns": { "createdBy": "UUID", "createdAt": "Start Date", "updatedAt": "Updated Date", "completedDate": "Completed Date", "organisationName": "Organisation Name", "organisationId": "Organisation Id", "surveyName": "Survey Name", "surveyId": "Survey Id", "surveySubmissionId": "Survey Submission Id", "criteriaExternalId": "Criteria External Id", "criteriaId": "Criteria Id", "criteriaName": "Criteria Name", "evidences": "Evidences", "remarks": "Remarks", "isAPrivateProgram": "Private Program", "questionExternalId": "Question External Id", "questionName": "Question", "questionResponseLabel": "Answer", "questionECM": "Question ECM", "questionId": "Question Id", "questionResponseType": "Question Response Type", "solutionExternalId": "Solution External Id", "solutionId": "Solution Id", "solutionName": "Solution Name" },
      "sortingColumns": "UUID,Start Date,Updated Date,Completed Date,Organisation Id,Organisation Name,Survey Id,Survey Name,Survey Submission Id,Criteria External Id,Criteria Id,Criteria Name,Private Program,Question External Id,Question Id,Question,Answer,Question ECM,Question Response Type,Evidences,Remarks,Solution External Id,Solution Id,Solution Name"
   }
}
```

**Observation Status Report:**
```json
{
   "report": "observationStatusReport",
   "config": {
      "reportColumns": { "createdBy": "UUID", "createdAt": "Observation Started Date", "solutionExternalId": "Solution External Id", "solutionId": "Solution Id", "solutionName": "Solution Name", "solutionType": "Solution Type", "observationName": "Observation Name", "observationId": "Observation Id", "observationSubmissionId": "Observation Submission Id", "isAPrivateProgram": "Private Program" },
      "sortingColumns": "UUID,Solution External Id,Solution Id,Solution Name,Solution Type,Observation Id,Observation Name,Observation Submission Id,Private Program,Observation Started Date,Status of Submission,Submission Date"
   }
}
```

**Observation Question Report:**
```json
{
   "report": "observationQuestionReport",
   "config": {
      "reportColumns": { "createdBy": "UUID", "createdAt": "Start Date", "updatedAt": "Updated Date", "completedDate": "Completed Date", "organisationName": "Organisation Name", "organisationId": "Organisation Id", "observationName": "Observation Name", "observationId": "Observation Id", "observationSubmissionId": "Observation Submission Id", "entity": "Entity", "entityType": "Entity Type", "isRubricDriven": "Rubric Driven", "criteriaLevelReport": "Criteria Level Report", "criteriaDescription": "Criteria Description", "criteriaExternalId": "Criteria External Id", "criteriaId": "Criteria Id", "criteriaName": "Criteria Name", "course": "Course", "remarks": "Remarks", "isAPrivateProgram": "Private Program", "questionExternalId": "Question External Id", "questionName": "Question", "questionResponseLabel": "Answer", "questionECM": "Question ECM", "questionId": "Question Id", "questionResponseType": "Question Response Type", "solutionExternalId": "Solution External Id", "solutionId": "Solution Id", "solutionDescription": "Solution Description", "solutionName": "Solution Name" },
      "sortingColumns": "UUID,Start Date,Updated Date,Completed Date,Organisation Id,Organisation Name,Observation Id,Observation Name,Observation Submission Id,Entity,Entity Type,Rubric Driven,Criteria Level Report,Criteria Description,Criteria External Id,Criteria Id,Criteria Name,Private Program,Course,Question External Id,Question Id,Question,Answer,Question ECM,Question Response Type,Remarks,Solution External Id,Solution Id,Solution Description,Solution Name"
   }
}
```

### Step 3: Run Spark Reports
1. Run Spark report jobs via **crontab**.
2. Check logs:
   ```
   cd /mount/data/analytics/scripts/logs/
   cat joblog.log
   ```

### Step 4: Access Reports
1. If the job runs successfully without errors, reports can be accessed on the **SPV portal**.

