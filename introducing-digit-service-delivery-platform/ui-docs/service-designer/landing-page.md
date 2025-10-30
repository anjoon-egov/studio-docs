# Landing Page

**Objective:** <mark style="color:red;">The</mark> <mark style="color:red;"></mark><mark style="color:red;">**Landing Page**</mark> <mark style="color:red;"></mark><mark style="color:red;">serves as the entry point for administrators to manage existing service definitions and create new service configurations efficiently within the Service Designer module.</mark>

<figure><img src="../../../.gitbook/assets/unknown (2).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

#### **1. What the User Sees**

When the **Service Designer** module is opened, the **Landing Page** displays two primary sections:

* **List of Published Services** – Services currently live and accessible on the citizen-facing portal.
* **List of Drafted Services** – Services that are being configured or duplicated but not yet published.

Each service entry includes the service name, module name, status (draft/published), and available user actions.

#### **2. Actions Available**

Users can perform the following key actions directly from the landing page:

* **Create Service**\
  Opens a popup to provide:
  * **Module Name**
  * **Service (Master) Name**\
    After confirmation, a new **drafted service** is created with empty configurations.
* **Duplicate Service**\
  Allows users to clone an existing **published service** by providing:
  * A new **Module Name**
  * A new **Service Name**\
    The duplicated service appears in the **Drafted Services** list and can be edited independently.
* **Import Service**\
  Enables importing of an existing service configuration using a **JSON configuration**.\
  The user pastes the configuration and provides:
  * **Module Name**
  * **Master Name**\
    This action creates a new **draft service** pre-populated from the imported JSON data.

#### **3. Sequence Flow**

1. When the landing page loads, the following API calls are triggered:
   * `mdmsAPI (Studio.ServiceConfigurationDrafts)`
   * `Public Service Search API`
2. The responses from these APIs are processed to populate the two lists:
   * **Published Services** → From the Public Service Search API
   * **Drafted Services** → From the MDMS API results that **do not exist** in the published list.
3. The user is then presented with:
   * A **grid/table view** separating Published and Drafted services
   * **Action buttons** (Create, Duplicate, Import)
4. **Action Behaviour:**
   * **Create Service** → Opens popup → Creates empty draft entry
   * **Duplicate Service** → Prompts new module/service names → Creates draft copy
   * **Import Service** → Opens import dialog → Accepts JSON config → Creates draft
5. When a user clicks any **published service**, they are redirected to the **corresponding service’s application page** on the app side for live interaction or testing.



## Technical Implementation

* **Pages:**
  * LandingPage.js : handle whole logic of displaying the landing page, based on a config so configurable in nature.&#x20;
* **State Management:**\
  Uses React state to maintain service lists and popup states.
* **API Calls:**
  * `MDMS: /egov-mdms-service/v1/_search (Studio.ServiceConfigurationDrafts)`
  * `MDMS: /egov-mdms-service/v1/_create (Studio.ServiceConfigurationDrafts)`
  * `Public Service Search API: /service/v1/_search`
* **Filtering Logic:**\
  Drafted services are identified as those present in **Studio.ServiceConfigurationDrafts** but not in the **public service API response**.



## MDMS Data&#x20;

This screen relies on **Studio.ServiceConfigurationDrafts** from MDMS for fetching draft service metadata such as below and to create the empty draft in case of new service and pre-populated draft in case of duplicate or import of a service:

* Service name
* Module
* Last modified date
* Creator info



## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.



## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>STUDIO_DESIGNER</td><td>/public-service-init/v1/service</td><td>All the published services</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr></tbody></table>

