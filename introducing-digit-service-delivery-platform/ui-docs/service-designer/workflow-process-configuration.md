# Workflow / Process Configuration

**Objective:** <mark style="color:red;">This screen enables administrators to visually design the complete process flow for a service — including states, actions, transitions, and role responsibilities.</mark>

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* A **visual process designer** canvas with Start, Process, and End states.
* Toolbar with controls to add states or transitions.
* Button **“Get Service Config”** to generate and preview the final workflow JSON.

**Actions Available**

* **Create Workflow** → Add and connect states to define process sequence.
* **Configure State Properties** → Set name, description, forms, checklists, and notifications.
* **Configure Action Properties** → Assign action name, description, roles, and workflow model config.
* **Generate Configuration** → Export workflow JSON for deployment.

**Sequence Flow**

1. The user opens the designer, which initializes an empty workflow.
2. States and transitions are added to define process logic.
3. Each state and action is configured with metadata.
4. Clicking **Get Service Config** generates the complete workflow JSON.
5. The user can choose to **Save as Draft** or **Publish Service**.

## Technical Implementation

* Uses the uiworkflow key in MDMS.
* Workflow data stored as connected graph structures representing states and transitions.
* Integrated with DIGIT’s backend to export final service configurations.

**Pages / Components:**

* `workflowPage.js` – includes canvas renderer, node editor, and state/action property panels.

**State Management:**

* Uses **React state** to track workflow nodes, transitions, and selected configuration details.

## MDMS Data&#x20;

Studio.ServiceConfigurationDrafts.uiroles is getting used to manage the multiple checklist creation, edition etc.

## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr><tr><td>SERVICE_DESIGNER</td><td>/public-service-init/v1/service</td><td>This is trigger the init of the service once publish is clicked</td></tr></tbody></table>
