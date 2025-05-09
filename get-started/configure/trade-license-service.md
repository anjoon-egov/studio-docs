# Trade License Service

This configuration file defines the service configuration for Trade License (Tradelicence) under the CommonService module in an MDMS (Master Data Management System). Below is a breakdown of its key sections:

### 1. General Information

* tenantId: "pg" → The tenant or jurisdiction the configuration applies to.
* moduleName: "CommonService" → Indicates that this configuration is part of the common services.

### 2. Service Details (ServiceConfiguration)

This section defines the Trade License service and its various configurations.

#### **2.1 Fields Configuration**

This defines the different attributes required for trade licenses:

* Each field has a name, label, type, and reference.
* Some fields depend on others (e.g., tradeStructureSubType depends on tradeStructureType).
* Trade Name (tradeName) has validations:
  * Length (2-128 characters)
  * Regex validation (^\[A-Za-z0-9 ]+$) – allows only letters, numbers, and spaces.
  * Mandatory field (isRequired: true).

#### 3. Workflow Configuration

Defines the workflow stages:

* businessService: "NewTL" (Trade License application process).
* ACTIVE status: "APPROVED" → License is approved.
* INACTIVE statuses: "REJECTED", "WITHDRAWN" → License is rejected or withdrawn.

#### 4. Fee Calculation (calculator)

Defines billing slabs:

* applicationFee: ₹2000 is the base fee for applying.

#### 5. ID Generation (idgen)

* Trade license application numbers follow the format: "tl.application.number".

#### 6. Localisation (localisation)

* Uses localisation from the "digit-tradelicence" module.

#### 7. Notifications (notification)

* SMS and email notifications exist but are not yet defined ("TODO" placeholders).

#### 8. Access Control (access)

* Roles: "TL\_CREATOR" has access.
* Actions: The only defined action is calling tl-services/v1/create.

#### 9. Rules & Validations (rules)

Defines different types of rules for:

* Validation (schema, api, custom, or none).
* Fee Calculation (api, custom, or none).
* Registry handling (api or none).
* References: The "initiate" service triggers tradelicence.

#### 10. Document Requirements (documents)

Defines documents needed for application:

1. Address Proof: Aadhaar, Voter ID (optional).
2. Identity Proof: Aadhaar, Voter ID (mandatory).
3. Owner's Photo: JPEG, PNG format (mandatory).

Common restrictions:

* Allowed file types: PDF, DOC, XLS, JPEG, PNG.
* Max file size: 5MB.

#### 11. PDF Configuration (pdf)

Defines PDF documents for:

* Application: "tl-application".
* Bill: "tl-bill".

Receipt: "tl-receipt".

#### 12. Billing (bill)

* Service for handling application fees: "ApplicationFee".

#### 13. Payment (payment)

* Payment gateway: Placeholder ("TODO", to be configured).

#### 14. API Configurations (apiconfig)

Defines API endpoints for:

* Registering trade licenses (/tl-services/v1/create).
* Searching trade licenses (/tl-services/v1/search).
* API methods: POST.
* Host: Can be staging (https://staging.digit.org) or local (http://tl-services.egov:8080).

#### 15. Applicant Rules (applicant)

* Minimum 1 and maximum 3 applicants allowed.
* Applicant types: "individual" or "organisation".

#### 16. Boundary Configuration (boundary)

* Hierarchy Type: "REVENUE".
* Lowest boundary level: "locality".

#### 17. Enabling Access (enabled)

* Available for citizen and employee users
