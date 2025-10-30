# Search Screen

**Objective:** <mark style="color:red;">This allows users to find existing service applications using specific identifiers such as Application Number, Applicant Name, or Mobile Number.</mark>

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* A **search form** with configurable fields.
* A **search button** that triggers results in a tabular format.
* Each result row includes key identifiers and links to the **View Screen**.

**Key Features**

* Search parameters dynamically derived from Inbox configuration (`searchParams`).
* Results rendered in real time with pagination support.
* Quick navigation to View Screen for full details.

## Technical Implementation

* Uses `searchParams` configuration from the **genericSearchConfig** schema.
* Calls `/service/public/_search` to fetch matching records.
* Supports client-side caching for repeat searches.

**Pages / Components:**

* `DigitDemoSearch.js` – maintain module and service singularity for generic Search Screen.
* InboxSearchComposer : common component to render DIGIT Inbox screen.

**State Management:**

* Uses **React state** for criteria selection and results pagination.

## Localisation

The localisations modules gettting "digit-boundary-admin", "digit-boundary-NEWTEST00222", studio-{module}.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service-init/v1/service (GET)</td><td>This gets the service list associsated with each module</td></tr><tr><td>STUDIO_ADMIN</td><td>egov-workflow-v2/egov-wf/businessservice/_search</td><td>This helps in getting the business service and workflow state.</td></tr><tr><td>STUDIO_ADMIN</td><td>/access/v1/actions/mdms/_get</td><td>This helps is maintaining role action access controls</td></tr><tr><td>STUDIO_ADMIN</td><td>/inbox/v2/_search</td><td>This gets the list of items for Inbox</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service/v1/application/{serviceCode} (GET)</td><td>This helps in getting the application data related to search params</td></tr></tbody></table>
