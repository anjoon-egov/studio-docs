# Apply

**Objective:** <mark style="color:red;">This enables users to submit new applications for a service, capturing all required details as defined in the Service Designer’s configuration.</mark>

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* A **stepper-based form** layout where each step corresponds to a section (Applicant, Address, Document, or Custom sections).
* Fields marked as mandatory or optional based on configuration.
* Document upload and preview section.
* Submit button to initiate the workflow.

**Key Features**

* Stepper-driven form flow that mirrors the service configuration structure.
* Automatic validation for field types, lengths, and mandatory requirements.
* Auto-generation of **Application Numbers** via IDGEN integration.
* File upload and validation per **Document Configuration**.
* Seamless transition to the View Screen upon successful submission.

## Technical Implementation

* Uses the **`formData`** and **`workflow`** definitions from the Service Configuration JSON.
* Each step dynamically rendered using `FormComposerV2` and `FieldV1` components.
* Client-side validations implemented using `react-hook-form`.
* Auto ID generation via `egov-idgen` service integration.

**Pages / Components:**

* `DigitDemoComponent` – main page rendering stepper and submit logic..
* UploadAndDownloadDocumentHandler.js – This handles the logic of downloading a document and uploading a document for the same, similarly only downloading the document during the form step.
* MultiChildFormWrapper.js is introduced to help with capturing multiple data in a single section in an array form.

**State Management:**

* Uses **React state** to track form data, navigation between steps, and submission progress.
* Global config is used for other configuration of an instance eg [UAT Global config](https://egov-uat-assets.s3.ap-south-1.amazonaws.com/globalConfigsStudio.js)

## MDMS Data

Studio.ServiceConfiguration is getting used to define the whole form structure along with validations.

## Localisation

The localisations modules gettting "digit-boundary-admin", "digit-boundary-NEWTEST00222", studio-{module}.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service-init/v1/service (GET)</td><td>This is trigger the init of the service once publish is clicked</td></tr><tr><td>STUDIO_ADMIN</td><td>egov-workflow-v2/egov-wf/businessservice/_search</td><td>This helps in getting the business service and workflow state.</td></tr><tr><td>STUDIO_ADMIN</td><td>/access/v1/actions/mdms/_get</td><td>This helps is maintaining role action access controls</td></tr><tr><td>STUDIO_ADMIN</td><td>/public-service/v1/application/{serviceCode}</td><td>This is the apply url which creates the application in database.</td></tr></tbody></table>
