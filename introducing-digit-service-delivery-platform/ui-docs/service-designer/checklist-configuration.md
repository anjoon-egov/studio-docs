# Checklist Configuration

**Objective**<mark style="color:red;">**:**</mark> <mark style="color:red;"></mark><mark style="color:red;">This</mark> <mark style="color:red;">page allows administrators to define structured validation or verification checklists that are used during service processing or approval, These checklists ensure consistency in inspection, verification, and approval steps.</mark>

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

The screen displays:

* A **list of existing checklists**, each showing its name, description, and question count.
* A **“Create Checklist”** option that opens a new checklist designer.

**Actions Available**

* **Create Checklist:**\
  Opens a clean checklist builder.\
  The user can:
  * Define a **Checklist Name** and **Help Text**.
  * Add multiple **Questions**, which can be of type **radio** (Yes/No) or **text**.
  * Add nested sub-questions for conditional validation steps.
* **Edit Checklist:**\
  Opens an existing checklist for modification, allowing users to add, remove, or reorder questions.

**Sequence Flow**

1. On load, the system calls:
   * `mdmsAPI (Studio.ServiceConfigurationDrafts)` to fetch existing checklists.
2. The response from the `uiChecklist` key populates the list.
3. Each checklist includes:
   * Metadata like name, help text.
   * A structured question list.
4. When saved, the checklist data is persisted in MDMS draft storage.
5. Users can re-edit checklists from the landing page at any time.

## Technical Implementation

* Uses the `uiChecklist` key in MDMS.
* Each checklist and its question hierarchy stored as JSON metadata.
* The checklist designer UI renders nested question structures dynamically using React components.

**Pages / Components:**

* `checklist` – includes components for question creation, nested question UI, and checklist preview.

**State Management:**

* Uses **React state** to manage checklist data, active question editing, and form submission flow.

## MDMS Data&#x20;

Studio.ServiceConfigurationDrafts.uiChecklist is getting used to manage the multiple checklist creation, edition etc.

## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr></tbody></table>
