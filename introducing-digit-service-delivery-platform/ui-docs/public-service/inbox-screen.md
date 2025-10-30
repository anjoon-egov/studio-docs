# Inbox Screen

**Objective:** <mark style="color:red;">This provides a centralized dashboard for employees or stakeholders to view and manage submitted applications assigned to them.</mark>

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* A **tabular list** of submitted applications with columns like Application Number, Applicant Name, Status, and Date.
* Filters and search options to refine visible records.
* Clickable rows redirecting to the **View Screen** for detailed access.

**Key Features**

* Configurable **filters** and **columns** based on Inbox configuration.
* Pagination and sorting for large datasets.
* Role-based visibility to ensure users only view relevant applications.
* Real-time synchronization with backend workflow states.

## Technical Implementation

* Uses generic Inbox configuration to show data module wise, this configuration in turn is passed to inboxSearchComposer for the rendering of the screen.
* Fetches data via `/inbox/v1/_search` and `/service/public/_search` APIs.
* Filters and pagination implemented client-side for performance optimization.

**Pages / Components:**

* `inboxService.js` – maintain module and service singularity for generic inbox screen.
* InboxSearchComposer : common component to render DIGIT Inbox screen.

**State Management:**

* Uses **React state** and DIGIT’s centralized store for pagination, sorting, and selected filters.

## MDMS Data

Studio.ServiceConfiguration is getting used to get the data if parallel workflow is present, and accordingly update business service and ui configuration.

## Localisation

The localisations modules gettting "digit-boundary-admin", "digit-boundary-NEWTEST00222", studio-{module}.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service-init/v1/service (GET)</td><td>This gets the service list associsated with each module</td></tr><tr><td>STUDIO_ADMIN</td><td>egov-workflow-v2/egov-wf/businessservice/_search</td><td>This helps in getting the business service and workflow state.</td></tr><tr><td>STUDIO_ADMIN</td><td>/access/v1/actions/mdms/_get</td><td>This helps is maintaining role action access controls</td></tr><tr><td>STUDIO_ADMIN</td><td>/inbox/v2/_search</td><td>This gets the list of items for Inbox</td></tr><tr><td>STUDIO_ADMIN</td><td>/egov-location/location/v11/boundarys/_search</td><td>This helps in getting the heirarchial data details for location dropdown etc</td></tr></tbody></table>
