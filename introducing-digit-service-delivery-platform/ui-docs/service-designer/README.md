# Service Designer

**Objective:** <mark style="color:red;">The Service Designer module provides a no-code interface for administrators to configure and publish new services on the DIGIT platform by defining workflows, roles, forms, and notifications seamlessly.</mark>

## About Module

The **Service Designer** enables a department or administrator to design and publish a complete digital service through the following flow:

1. **Module Access**
   * The user logs into DIGIT Studio and navigates to the **Service Designer** module from the main dashboard.
   * The landing page lists all existing service definitions with options to **create** new service or edit existing drafted service.
2. **Create a New Service**
   * Clicking **“Create Service”** opens a guided setup where the administrator provides:
     * **Module name :** this is the service group
     * **Service name :** this is the unique identifier for the particular service.
3. **Define Forms**
   * Administrators configure **dynamic forms** that capture service-specific data from end-users.
   * Each field can be defined with:
     * Field type (text, dropdown, date, etc.)
     * Validation rules
     * Mandatory/optional flag
     * Default values or MDMS data source references
4. **Define Checklists**
   * Add **checklists** that are used for verification or approval at different stages of the service workflow.
5. **Configure Roles**
   * Create or assign **roles** responsible for handling various workflow stages.
   * Each role is mapped with permissions and actions like &#x43;_&#x72;eator, Viewer or Editor._
   * Role can be edited to change description or access.
6. **Workflow Configuration**
   * Define the **service workflow** — including:
     * Process **states** (e.g., Draft, Submitted, Verified, Approved)
     * **Transitions** between states
     * **Actions** allowed at each stage
     * Role responsible for each transition
     * notification, chechlist added for each state or action.
   * Each transition can trigger **automated tasks** or **notifications.**
7. **Notifications**
   * Configure **automated notifications** that are sent at key workflow events:
     * Submission confirmation
     * Approval or rejection alerts
     * Escalation notifications etc
   * Notifications can be via **email or** **SMS**, using pre-defined templates.
8. **Preview and Publish**
   * Before publishing, users can **preview** the workflow to validate configurations.
   * Once verified, the service definition is **published** and becomes available to end-users through the DIGIT front-end portal.

This streamlined process allows administrators to launch or update services quickly without developer involvement, ensuring agility in public service delivery.

