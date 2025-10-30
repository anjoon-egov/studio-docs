# Form Configuration

**Objective:** <mark style="color:red;">Allows users to create and manage forms for capturing service-related data dynamically.</mark>

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* List of existing forms
* Option to **create a new form**

**Actions Available**

* **Create Form** → Opens a popup where the user provides **Form Name** and **Description**.
* **Edit Form** → Opens the **Form Designer** with prefilled values, allowing updates and resubmission.

**Sequence Flow**

1. On page load, the system calls:
   * `mdmsAPI (Studio.ServiceConfigurationDrafts)` to fetch existing forms.
2. Forms are displayed using the `uiforms` section of `Studio.ServiceConfigurationDrafts`.
3. User can:
   * Create new forms → Adds an empty drafted form.
   * Edit existing forms → Opens in designer for modification.
4. Each form can contain multiple **sections**:
   * Custom sections
   * Applicant, Address, and Document sections
5. Within each section:
   * Add various **field types** with configurable params like label, validation and properties.
   * Field metadata is managed via MDMS configs:
     * `FieldTypeMappingConfig`
     * `FieldPropertiesPanelConfig`

## Technical Implementation

* Uses the `uiforms` key in MDMS.
* Field configuration and metadata fetched from `FieldTypeMappingConfig` and `FieldPropertiesPanelConfig`.
* Dynamic field rendering driven by FormComposerV2 and FieldV1 components.
* **Pages/ components:**
  * formbuilder : has all the page component related to form designer like sidepanel, preview component, add field popup etc.
* **State Management:**\
  Uses React state to maintain service lists and popup states.

## MDMS Data&#x20;

these are the masters getting used for form designer component :

* `Studio.ServiceConfigurationDrafts`
* `FieldTypeMappingConfig`
* `FieldPropertiesPanelConfig`

## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr></tbody></table>

