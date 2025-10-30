# View Screen

**Objective:** <mark style="color:red;">This displays complete details of a selected application — including all data captured during application, workflow history, and available actions.</mark>

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* A **detailed application summary** including Applicant Info, Application ID, and Submission Date.
* Uploaded documents with download options.
* Current workflow status and history timeline.
* Conditional buttons (e.g., Approve, Reject, Send Back) based on user role and workflow state.

**Key Features**

* Auto-rendered fields from the **Apply Form Configuration**.
* Integrated document viewer with downloadable links.
* Role-based visibility for action buttons.



## Technical Implementation

* Consumes published Service Configuration to render sections.
* Fetches workflow history and current state from `/workflow/process/_search`.
* PDF generation handled via `/pdf-service/v1/_create`.

**Pages / Components:**

* digitDemoViewComponent.js – main container for application details.
* `workflowTimeline.js` – visual history tracker.
* `actionFooter.js` – handles dynamic action rendering and submission.

**State Management:**

* Uses **React state** and DIGIT global store to maintain application data, workflow context, and user actions.

## MDMS Data

Studio.ServiceConfiguration is getting used to define the whole View structure along with how the value has to be shown.

## Localisation

The localisations modules gettting "digit-boundary-admin", "digit-boundary-NEWTEST00222", studio-{module}.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service-init/v1/service (GET)</td><td>This helps in getting the module and services generated.</td></tr><tr><td>STUDIO_ADMIN</td><td>egov-workflow-v2/egov-wf/businessservice/_search</td><td>This helps in getting the business service and workflow state.</td></tr><tr><td>STUDIO_ADMIN</td><td>/access/v1/actions/mdms/_get</td><td>This helps is maintaining role action access controls</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service/v1/application/{serviceCode}</td><td>This is the GET url which fetched the application from database.</td></tr></tbody></table>
