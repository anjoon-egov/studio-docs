# Notification Configuration

**Objective:** <mark style="color:red;">This page allows administrators to define automated notifications (Email or SMS) that are triggered at specific workflow events like submission, approval, or rejection.</mark>

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## Workflow Details

**What the User Sees**

* Tabs for **Email** and **SMS** notifications.
* A list of notifications with message type, subject, and trigger event.
* Action buttons to **Create** or **Edit** notifications.

**Actions Available**

* **Create Notification** → Opens a configuration form to specify notification type, message content, and dynamic parameters.
* **Edit Notification** → Opens an existing configuration for modification.

**Sequence Flow**

1. On page load, system calls `mdmsAPI (Studio.ServiceConfigurationDrafts)` to fetch `uiNotification`.
2. Users can add new notifications or edit existing ones.
3. Dynamic placeholders can be inserted from form field keys to personalize messages.

## Technical Implementation

* Uses the `uiNotification` key in MDMS.
* Notification templates stored as structured JSON metadata.
* Each template supports placeholders and type-based rendering for Email or SMS.

**Pages / Components:**

* `Notification` – includes notification form, message preview, and parameter mapping interface.

**State Management:**

* Uses **React state** to track active template, type selection, and dynamic parameter mapping.

## MDMS Data&#x20;

Studio.ServiceConfigurationDrafts.uiNotification is getting used to manage the multiple checklist creation, edition etc.

## Localisation

This module uses the **`rainmaker-servicedesigner`** localisation bundle. All text labels, tooltips, and button names are stored under this module key.

## API Role Action Mapping

<table data-header-hidden><thead><tr><th></th><th width="298"></th><th></th></tr></thead><tbody><tr><td><strong>Role</strong></td><td><strong>API Endpoint / Action</strong></td><td><strong>Description</strong></td></tr><tr><td>MDMS_ADMIN</td><td>/egov-mdms-service/v1/_search</td><td>Searching of MDMS</td></tr><tr><td>OPEN</td><td>/localization/messages/v1/_search</td><td>Getting the localisation messages</td></tr><tr><td>MDMS_ADMIN</td><td>egov-mdms-service/v2/_create</td><td>to create service configuration draft incase of duplicate or import or new service creation.</td></tr></tbody></table>
