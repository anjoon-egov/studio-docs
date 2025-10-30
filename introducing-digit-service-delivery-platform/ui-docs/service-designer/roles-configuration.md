# Roles Configuration

**Objective:** <mark style="color:red;">This screen allows administrators to define role-based access and permission structures, determining which users can create, edit, or review service workflows.</mark>

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* List of existing roles with name, description, and assigned access levels.
* Buttons to **Create Role** or **Edit Role**.

**Actions Available**

* **Create Role** → Opens popup with fields for Role Name, Description, and Access Permissions (creator, editor, viewer).
* **Edit Role** → Opens side panel with pre-filled values for modification.

**Sequence Flow**

1. On load, API call to `mdmsAPI (Studio.ServiceConfigurationDrafts)` retrieves data from the `uiroles` key.
2. The list of configured roles is displayed.
3. User can create or edit role metadata; updates reflect instantly on the landing page.

## Technical Implementation

* Uses the `uiroles` key in MDMS.
* Role metadata stored as JSON with attributes like `roleName`, `description`, and `accessType`.

**Pages / Components:**

* `roles.js` – includes the main list view, create/edit popups, and permission selector.

**State Management:**

* Uses **React state** to manage selected role, modal visibility, and permission toggling.

## MDMS Data&#x20;

Studio.ServiceConfigurationDrafts.uiroles is getting used to manage the multiple checklist creation, edition etc.

## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr></tbody></table>

##
