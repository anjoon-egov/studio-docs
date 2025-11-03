# Public-service-init

PUBLIC SERVICE INIT - API DOCUMENTATION

***

### TABLE OF CONTENTS

1. Overview
2. Create Service API
3. Search Service API
4. Update Service API
5. Error Handling
6. Kafka Integration
7. External Dependencies
8. Security & Best Practices
9. Data Structures
10. Appendix

***

### SECTION 1: OVERVIEW

This documentation covers the Public Service Init microservice APIs that manage public service definitions in the DIGIT platform. These APIs handle service creation, searching, and configuration updates with full multi-tenant support.

#### Key Capabilities

* Create new public service definitions with auto-generated service codes
* Search services with flexible filtering and pagination
* Update service configurations with change detection
* Multi-language support via localization
* Role-based access control management
* Workflow state management
* Billing and demand configuration
* Checklist management

#### Service Architecture

The service follows a layered architecture:

* Controller Layer: HTTP request/response handling
* Service Layer: Business logic and orchestration
* Repository Layer: Data persistence via Kafka and database queries

***

### SECTION 2: CREATE SERVICE API

#### 2.1 Endpoint Overview

| Field          | Value                           |
| -------------- | ------------------------------- |
| HTTP Method    | POST                            |
| Path           | /public-service-init/v1/service |
| Authentication | X-Tenant-Id header required     |
| Response Type  | JSON                            |

#### 2.2 Required Headers

| Header       | Type   | Required | Description                         |
| ------------ | ------ | -------- | ----------------------------------- |
| X-Tenant-Id  | string | Yes      | Tenant identifier for multi-tenancy |
| Content-Type | string | Yes      | Must be application/json            |
| auth-token   | string | No       | Authentication token                |

#### 2.3 Request Body Structure

{

&#x20; "RequestInfo": {

&#x20;   "apiId": "string",

&#x20;   "ver": "string",

&#x20;   "ts": "timestamp (milliseconds)",

&#x20;   "action": "string",

&#x20;   "did": "string",

&#x20;   "key": "string",

&#x20;   "msgId": "string",

&#x20;   "authToken": "string",

&#x20;   "userInfo": {

&#x20;     "id": "string",

&#x20;     "uuid": "uuid",

&#x20;     "userName": "string",

&#x20;     "name": "string",

&#x20;     "mobileNumber": "string",

&#x20;     "emailId": "string"

&#x20;   }

&#x20; },

&#x20; "Service": {

&#x20;   "tenantId": "string",

&#x20;   "module": "string",

&#x20;   "businessService": "string",

&#x20;   "status": "ACTIVE",

&#x20;   "additionalDetails": {

&#x20;     "key1": "value1"

&#x20;   }

&#x20; }

}

\


#### 2.4 Response Structure

{

&#x20; "ResponseInfo": {

&#x20;   "apiId": "string",

&#x20;   "ver": "string",

&#x20;   "status": "SUCCESSFUL"

&#x20; },

&#x20; "Services": \[

&#x20;   {

&#x20;     "id": "uuid",

&#x20;     "tenantId": "string",

&#x20;     "module": "string",

&#x20;     "businessService": "string",

&#x20;     "status": "ACTIVE",

&#x20;     "serviceCode": "AUTO\_GENERATED",

&#x20;     "additionalDetails": {},

&#x20;     "version": 1,

&#x20;     "auditDetails": {

&#x20;       "createdBy": "uuid",

&#x20;       "lastModifiedBy": "uuid",

&#x20;       "createdTime": "timestamp",

&#x20;       "lastModifiedTime": "timestamp"

&#x20;     }

&#x20;   }

&#x20; ]

}

\


#### 2.5 Detailed Step-by-Step Flow

**Step 1: Controller Layer Entry Point**

Method: CreateServiceHandler() in controller/public\_controller.go:34

| Step | Action          | Details                                                                   |
| ---- | --------------- | ------------------------------------------------------------------------- |
| 1a   | Decode JSON     | Parse request body into ServiceRequest struct, return HTTP 400 if invalid |
| 1b   | Validate Header | Check for X-Tenant-Id, return HTTP 400 if missing                         |
| 1c   | Assign Tenant   | If req.Service.TenantId empty, assign from header                         |

**Step 2: Enrich Service with ID Generation**

Method: EnrichmentService.EnrichServiceWithIdGen() in service/enrichment\_service.go:27

| Step | Action              | Details                                                                                        |
| ---- | ------------------- | ---------------------------------------------------------------------------------------------- |
| 2a   | Get MDMS Config     | Search MDMS for service configuration using schema SERVICE\_MODULE\_NAME.SERVICE\_MASTER\_NAME |
| 2b   | Extract ID Format   | Get idgen configuration array, extract format and name                                         |
| 2c   | Register Format     | Call CreateMDMS() to register ID format in common-masters.IdFormat schema                      |
| 2d   | Generate ID         | Call external ID Generation Service to get unique service code                                 |
| 2e   | Create Action Tests | Generate action-test MDMS entries for API URLs and role mappings                               |

**Step 3: Validation**

Method: ValidationService.Validate() in service/validation\_service.go:39

| Step | Action                | Details                                                                                           |
| ---- | --------------------- | ------------------------------------------------------------------------------------------------- |
| 3a   | IDgen Validation      | Validate and create missing ID formats in MDMS                                                    |
| 3b   | Bill Validation       | Validate tax heads, tax periods, and business service config                                      |
| 3c   | Workflow Validation   | Create and validate workflow business service                                                     |
| 3d   | Localization Creation | Create message keys for: fields, workflow, SMS, checklists, documents, roles, applicants, citizen |
| 3e   | Process Persistence   | Publish validation results to Kafka process topic                                                 |

Localization Methods Called:

* BasicLocalization() - Form field labels
* Localization() - General UI constants
* WorkflowLocalization() - Workflow actions
* SMSLocalization() - Notification templates
* ChecklistLocalization() - Checklist items
* DocumentLocalization() - Document types
* RolesLocalization() - Role names
* ApplicantsDeatilsLocalization() - Applicant fields
* CitizenLocalization() - Citizen UI

**Step 4: Checklist Setup**

Method: ChecklistService.GetChecklist() in service/checklist\_service.go

| Step | Action            | Details                                                                         |
| ---- | ----------------- | ------------------------------------------------------------------------------- |
| 4a   | Get Config        | Fetch checklist configuration from MDMS using schema {module}.{businessService} |
| 4b   | Process Items     | For each checklist item, get sub-items from Studio.Checklists schema            |
| 4c   | Create Checklists | Create/update checklist definitions in Health Service for each state            |
| 4d   | Return Results    | Return map of checklist data or error                                           |

**Step 5: Get Final MDMS Configuration**

Method: MDMSV2Service.SearchMDMS()

| Step | Action   | Details                                              |
| ---- | -------- | ---------------------------------------------------- |
| 5a   | Search   | Get updated MDMS configuration after all validations |
| 5b   | Extract  | Get data map from first result                       |
| 5c   | Validate | Ensure config data exists and is valid               |

**Step 6: Service Creation**

Method: PublicService.CreateService() → PublicRepository.CreateService()

| Step | Action          | Details                                                                                                     |
| ---- | --------------- | ----------------------------------------------------------------------------------------------------------- |
| 6a   | Duplicate Check | Search existing services, return error if service already exists                                            |
| 6b   | Generate ID     | Create new UUID for service                                                                                 |
| 6c   | Set Audit       | Generate created\_by and created\_time                                                                      |
| 6d   | Publish Event   | Push service to SAVE\_PUBLIC\_SERVICE Kafka topic                                                           |
| 6e   | Version Mapping | Create ServiceVersionConfigMapping entry with MDMS data, publish to SAVE\_SERVICE\_VERSION\_CONFIG\_MAPPING |
| 6f   | Return Response | Build and return ServiceResponse with created service                                                       |

**Step 7: Return Response**

Method: CreateServiceHandler() (continued)

| Step | Action  | Details                              |
| ---- | ------- | ------------------------------------ |
| 7a   | Encode  | Marshal response to JSON             |
| 7b   | Headers | Set Content-Type: application/json   |
| 7c   | Status  | Return HTTP 200 with service details |

***

### SECTION 3: SEARCH SERVICE API

#### 3.1 Endpoint Overview

| Field          | Value                               |
| -------------- | ----------------------------------- |
| HTTP Method    | GET                                 |
| Path           | /public-service-init/v1/service     |
| Authentication | X-Tenant-Id and auth-token required |
| Response Type  | JSON                                |

#### 3.2 Required Headers

| Header      | Type   | Required | Description          |
| ----------- | ------ | -------- | -------------------- |
| X-Tenant-Id | string | Yes      | Tenant identifier    |
| auth-token  | string | Yes      | Authentication token |

#### 3.3 Query Parameters

| Parameter       | Type    | Required | Default     | Max  | Description                     |
| --------------- | ------- | -------- | ----------- | ---- | ------------------------------- |
| module          | string  | No       | -           | -    | Filter by module name           |
| businessService | string  | No       | -           | -    | Filter by business service      |
| serviceCode     | string  | No       | -           | -    | Filter by service code          |
| offset          | integer | No       | 0           | -    | Pagination offset (must be ≥ 0) |
| limit           | integer | No       | 1000        | 1500 | Results per page                |
| sortBy          | string  | No       | created\_at | -    | Sort field (see allowed values) |
| sortOrder       | string  | No       | DESC        | -    | ASC or DESC                     |

Allowed Sort Fields: id, created\_at, updated\_at, tenant\_id, module, business\_service, status, service\_code

#### 3.4 Response Structure

{

&#x20; "ResponseInfo": {

&#x20;   "status": "successful"

&#x20; },

&#x20; "Services": \[

&#x20;   {

&#x20;     "id": "uuid",

&#x20;     "tenantId": "string",

&#x20;     "module": "string",

&#x20;     "businessService": "string",

&#x20;     "status": "string",

&#x20;     "serviceCode": "string",

&#x20;     "additionalDetails": {},

&#x20;     "version": 1,

&#x20;     "auditDetails": {

&#x20;       "createdBy": "uuid",

&#x20;       "lastModifiedBy": "uuid",

&#x20;       "createdTime": "timestamp",

&#x20;       "lastModifiedTime": "timestamp"

&#x20;     }

&#x20;   }

&#x20; ]

}

\


#### 3.5 Detailed Step-by-Step Flow

**Step 1: Controller Layer Entry Point**

Method: SearchServiceHandler() in controller/public\_controller.go:176

| Step | Action          | Details                                                                                            |
| ---- | --------------- | -------------------------------------------------------------------------------------------------- |
| 1a   | Validate Header | Check X-Tenant-Id header, return HTTP 400 if missing                                               |
| 1b   | Extract Params  | Read all query parameters (module, businessService, serviceCode, offset, limit, sortBy, sortOrder) |

**Step 2: Offset Validation**

Method: SearchServiceHandler() (continued)

| Step | Action   | Details                                           |
| ---- | -------- | ------------------------------------------------- |
| 2a   | Parse    | Convert offset string to integer                  |
| 2b   | Validate | Return HTTP 400 if conversion fails or offset < 0 |
| 2c   | Set      | Default to 0 if not provided                      |

**Step 3: Limit Validation**

Method: SearchServiceHandler() (continued)

| Step | Action   | Details                                                          |
| ---- | -------- | ---------------------------------------------------------------- |
| 3a   | Parse    | Convert limit string to integer                                  |
| 3b   | Validate | Return HTTP 400 if: conversion fails, limit ≤ 0, or limit > 1500 |
| 3c   | Set      | Default to 1000 if not provided                                  |

**Step 4: Build Search Criteria**

Method: SearchServiceHandler() (continued)

| Step | Action         | Details                                                            |
| ---- | -------------- | ------------------------------------------------------------------ |
| 4a   | Initialize     | Create SearchCriteria struct with tenant ID                        |
| 4b   | Add Filters    | Conditionally add businessService, module, serviceCode if provided |
| 4c   | Set Sorting    | Add sortBy and sortOrder                                           |
| 4d   | Set Pagination | Add offset and limit                                               |

**Step 5: Service Search**

Method: PublicService.SearchService() → PublicRepository.SearchService()

| Step | Action        | Details                                                           |
| ---- | ------------- | ----------------------------------------------------------------- |
| 5a   | Build SQL     | Construct dynamic SQL query with base SELECT statement            |
| 5b   | WHERE Clause  | Add conditions based on search criteria filters                   |
| 5c   | Validate Sort | Check sortBy against whitelist, default to created\_at if invalid |
| 5d   | ORDER BY      | Add sorting: ORDER BY {sortBy} {sortOrder}, id {sortOrder}        |
| 5e   | LIMIT/OFFSET  | Add pagination clauses                                            |

**Step 6: SQL Injection Prevention**

| Measure               | Details                                       |
| --------------------- | --------------------------------------------- |
| Parameterization      | All user inputs use $1, $2, etc. placeholders |
| Whitelist Validation  | Sort columns validated against allowed list   |
| Sort Order Validation | Only ASC or DESC accepted                     |

**Step 7: Database Query Execution**

Method: PublicRepository.SearchService() (continued)

| Step | Action  | Details                                        |
| ---- | ------- | ---------------------------------------------- |
| 7a   | Execute | Run query with parameterized arguments         |
| 7b   | Iterate | For each row: scan data, unmarshal JSON fields |
| 7c   | Convert | Transform timestamps to UnixMilli format       |
| 7d   | Store   | Add service to map and results array           |
| 7e   | Close   | Defer rows.Close() for cleanup                 |

**Step 8: Response Construction**

Method: SearchServiceHandler() (final)

| Step | Action  | Details                             |
| ---- | ------- | ----------------------------------- |
| 8a   | Encode  | Marshal response to JSON            |
| 8b   | Headers | Set Content-Type: application/json  |
| 8c   | Status  | Return HTTP 200 with services array |

***

### SECTION 4: UPDATE SERVICE API

#### 4.1 Endpoint Overview

| Field          | Value                                         |
| -------------- | --------------------------------------------- |
| HTTP Method    | PUT                                           |
| Path           | /public-service-init/v1/service/{serviceCode} |
| Authentication | X-Tenant-Id header required                   |
| Response Type  | JSON                                          |

#### 4.2 Required Headers

| Header       | Type   | Required | Description       |
| ------------ | ------ | -------- | ----------------- |
| X-Tenant-Id  | string | Yes      | Tenant identifier |
| Content-Type | string | Yes      | application/json  |

#### 4.3 Path Parameters

| Parameter   | Type   | Required | Description                            |
| ----------- | ------ | -------- | -------------------------------------- |
| serviceCode | string | Yes      | Must match Service.serviceCode in body |

#### 4.4 Request Body Structure

{

&#x20; "RequestInfo": {

&#x20;   "apiId": "string",

&#x20;   "ver": "string",

&#x20;   "ts": "timestamp",

&#x20;   "userInfo": {

&#x20;     "uuid": "uuid"

&#x20;   }

&#x20; },

&#x20; "Service": {

&#x20;   "tenantId": "string",

&#x20;   "module": "string",

&#x20;   "businessService": "string",

&#x20;   "status": "ACTIVE",

&#x20;   "serviceCode": "MUST\_MATCH\_PATH",

&#x20;   "version": 1,

&#x20;   "additionalDetails": {}

&#x20; }

}

\


#### 4.5 Response Structure

{

&#x20; "ResponseInfo": {

&#x20;   "apiId": "string",

&#x20;   "ver": "string"

&#x20; },

&#x20; "Services": \[

&#x20;   {

&#x20;     "tenantId": "string",

&#x20;     "module": "string",

&#x20;     "businessService": "string",

&#x20;     "status": "string",

&#x20;     "serviceCode": "string",

&#x20;     "version": 2,

&#x20;     "additionalDetails": {},

&#x20;     "auditDetails": {

&#x20;       "lastModifiedBy": "uuid",

&#x20;       "lastModifiedTime": "timestamp"

&#x20;     }

&#x20;   }

&#x20; ]

}

\


#### 4.6 Detailed Step-by-Step Flow

**Step 1: Controller Layer Entry Point**

Method: UpdateServiceHandler() in controller/public\_controller.go:113

| Step | Action          | Details                                        |
| ---- | --------------- | ---------------------------------------------- |
| 1a   | Extract Path    | Get serviceCode from URL path parameter        |
| 1b   | Decode JSON     | Parse request body, return HTTP 400 if invalid |
| 1c   | Validate Header | Check X-Tenant-Id, return HTTP 400 if missing  |

**Step 2: Service Code Validation**

Method: UpdateServiceHandler() (continued)

| Step | Action      | Details                                                       |
| ---- | ----------- | ------------------------------------------------------------- |
| 2a   | Check Empty | Return HTTP 400 if req.Service.ServiceCode is empty           |
| 2b   | Check Match | Return HTTP 400 if path serviceCode ≠ req.Service.ServiceCode |

**Step 3: Get MDMS Configuration**

Method: UpdateServiceHandler() (continued)

| Step | Action           | Details                                                        |
| ---- | ---------------- | -------------------------------------------------------------- |
| 3a   | Construct Schema | Build schema code: SERVICE\_MODULE\_NAME.SERVICE\_MASTER\_NAME |
| 3b   | Build Filters    | Create filters with businessService and module                 |
| 3c   | Search MDMS      | Call MDMSV2Service.SearchMDMS()                                |
| 3d   | Extract Data     | Get data map from first result                                 |
| 3e   | Validate         | Return HTTP 500 if config missing or invalid                   |

**Step 4: Service Update**

Method: PublicService.UpdateService() → PublicRepository.UpdateService()

| Step | Action          | Details                                                         |
| ---- | --------------- | --------------------------------------------------------------- |
| 4a   | Existence Check | Search for existing service, return error if not found          |
| 4b   | Get Version     | Retrieve existing service version                               |
| 4c   | Increment       | Calculate new version: existing + 1                             |
| 4d   | Update Audit    | Set lastModifiedBy and lastModifiedTime                         |
| 4e   | Publish Event   | Push update to UPDATE\_PUBLIC\_SERVICE Kafka topic              |
| 4f   | Version Mapping | Create new ServiceVersionConfigMapping with incremented version |

**Step 5: Configuration Change Detection**

Method: PublicService.UpdateService() (continued) - Only if status ≠ "INACTIVE"

| Step | Action         | Details                                           |
| ---- | -------------- | ------------------------------------------------- |
| 5a   | Compare        | Call UpdateServiceHelper.CompareServiceConfigs()  |
| 5b   | Get Previous   | Retrieve config for version-1 from database       |
| 5c   | Detect Changes | Use reflect.DeepEqual() for root-level comparison |
| 5d   | Log Result     | Log detected changes with types and values        |

**Step 6: Configuration Change Processing**

Method: UpdateServiceHelper.ProcessConfigurationChanges()

| Step | Change Type  | Handler               | Actions                                            |
| ---- | ------------ | --------------------- | -------------------------------------------------- |
| 6a   | Bill         | billChanged()         | Placeholder for bill logic                         |
| 6b   | Workflow     | workflowChanged()     | Update business service, call WorkflowLocalization |
| 6c   | Notification | notificationChanged() | Update SMS notification templates                  |
| 6d   | Role         | roleChanged()         | Update role-action mappings in MDMS                |
| 6e   | Checklist    | checklistChanged()    | Update checklist definitions                       |
| 6f   | IDgen        | idgenChanged()        | Update ID generation formats in MDMS               |
| 6g   | Document     | documentChanged()     | Update document localization                       |
| 6h   | Form         | formChanged()         | Update form field localization                     |
| 6i   | Applicants   | applicantsChanged()   | Update applicant field localization                |
| 6j   | Citizen      | enabledChanged()      | Update citizen UI localization                     |
| 6k   | Other        | handleGenericChange() | Generic handler for unmapped changes               |

**Step 7: Response**

Method: UpdateServiceHandler() (final)

| Step | Action  | Details                                                    |
| ---- | ------- | ---------------------------------------------------------- |
| 7a   | Encode  | Marshal response to JSON                                   |
| 7b   | Headers | Set Content-Type: application/json                         |
| 7c   | Status  | Return HTTP 200 with updated service (version incremented) |

#### 4.7 Configuration Change Processing Details

**Workflow Change Handler**

workflowChanged()

├─→ Call workflowService.CreateAndValidateBusinessService(req)

│   └─→ Updates workflow business service in external Workflow Service

└─→ Call localizationService.WorkflowLocalization(mdmsConfigData, req)

&#x20;   └─→ Updates workflow state and action labels

\


**Role Change Handler**

roleChanged()

├─→ Search MDMS for action-test entry by URL

├─→ Get action ID for \`/public-service/v1/application/{serviceCode}\`

└─→ Call createMDMSRoleActionMapping()

&#x20;   ├─→ Create/update roles in MDMS

&#x20;   └─→ Create role-action mappings

\


**IDgen Change Handler**

idgenChanged()

├─→ Extract idgen list from MDMS config

└─→ For each idgen item:

&#x20;   ├─→ Search MDMS for existing ID format by idname

&#x20;   ├─→ Extract existing MDMS record

&#x20;   ├─→ Update with new type, format, idname

&#x20;   └─→ Call MDMSV2Service.UpdateMDMS()

\


**Checklist Change Handler**

checklistChanged()

├─→ Call checklistService.GetChecklist()

│   └─→ Creates/updates checklist definitions

└─→ Call localizationService.ChecklistLocalization()

&#x20;   └─→ Updates checklist label localization

\


***

### SECTION 5: ERROR HANDLING

#### 5.1 Error Response Format

{

&#x20; "Errors": \[

&#x20;   {

&#x20;     "code": "ERROR\_CODE",

&#x20;     "message": "Short error description",

&#x20;     "description": "Detailed error information"

&#x20;   }

&#x20; ]

}

\


#### 5.2 HTTP Status Codes

| Code | Meaning               | When Used                                   |
| ---- | --------------------- | ------------------------------------------- |
| 200  | OK                    | Successful operation                        |
| 400  | Bad Request           | Invalid parameters, validation failures     |
| 500  | Internal Server Error | Database, Kafka, or external service errors |

#### 5.3 Create Service Errors

| Error                                                                  | HTTP Code | Cause                    |
| ---------------------------------------------------------------------- | --------- | ------------------------ |
| X-Tenant-Id header is required                                         | 400       | Missing tenant header    |
| Invalid request payload                                                | 400       | Malformed JSON           |
| Service ID generation failed                                           | 500       | IDGen service error      |
| Failed to validate                                                     | 500       | Validation service error |
| Failed to fetch checklist                                              | 500       | Health service error     |
| MDMS data missing or invalid                                           | 500       | MDMS configuration error |
| Service already exists with same module, business service and tenantId | 400       | Duplicate service        |

#### 5.4 Search Service Errors

| Error                          | HTTP Code | Cause                  |
| ------------------------------ | --------- | ---------------------- |
| X-Tenant-Id header is required | 400       | Missing tenant header  |
| Invalid offset parameter       | 400       | Non-numeric offset     |
| Offset must be non-negative    | 400       | Negative offset value  |
| Invalid limit parameter        | 400       | Non-numeric limit      |
| Limit must be positive         | 400       | Zero or negative limit |
| Limit cannot exceed 1500       | 400       | Limit > 1500           |

#### 5.5 Update Service Errors

| Error                                              | HTTP Code | Cause                            |
| -------------------------------------------------- | --------- | -------------------------------- |
| X-Tenant-Id header is required                     | 400       | Missing tenant header            |
| Invalid request body                               | 400       | Malformed JSON                   |
| serviceRequest.Service.ServiceCode cannot be empty | 400       | Missing service code in body     |
| serviceCode in path and body are different         | 400       | Mismatch between URL and payload |
| No Service Found with given ServiceCode            | 500       | Service doesn't exist            |
| MDMS data missing or invalid                       | 500       | MDMS configuration error         |

***

### SECTION 6: KAFKA INTEGRATION

#### 6.1 Producer Topics

| Topic                                   | Purpose             | Producer Method    | Message Type         |
| --------------------------------------- | ------------------- | ------------------ | -------------------- |
| SAVE\_PUBLIC\_SERVICE                   | Create events       | CreateService      | ServiceRequest       |
| UPDATE\_PUBLIC\_SERVICE                 | Update events       | UpdateService      | ServiceRequest       |
| SAVE\_SERVICE\_VERSION\_CONFIG\_MAPPING | Version history     | Both Create/Update | VersionConfigMapping |
| SAVE\_PUBLIC\_SERVICE\_PROCESS          | Validation tracking | ValidationService  | ProcessData          |

#### 6.2 Message Structures

**Service Create/Update Message**

{

&#x20; "RequestInfo": { /\* ... \*/ },

&#x20; "Service": {

&#x20;   "id": "uuid",

&#x20;   "tenantId": "string",

&#x20;   "module": "string",

&#x20;   "businessService": "string",

&#x20;   "status": "string",

&#x20;   "serviceCode": "string",

&#x20;   "additionalDetails": {},

&#x20;   "version": 1,

&#x20;   "auditDetails": {

&#x20;     "createdBy": "uuid",

&#x20;     "lastModifiedBy": "uuid",

&#x20;     "createdTime": "timestamp",

&#x20;     "lastModifiedTime": "timestamp"

&#x20;   }

&#x20; }

}

\


**Version Config Mapping Message**

{

&#x20; "id": "uuid",

&#x20; "serviceCode": "string",

&#x20; "version": 1,

&#x20; "config": { /\* MDMS configuration data \*/ },

&#x20; "auditDetails": {

&#x20;   "createdBy": "uuid",

&#x20;   "createdTime": "timestamp"

&#x20; }

}

\


#### 6.3 Async Processing Pattern

The service uses an asynchronous persistence pattern via Kafka:

1. HTTP API → Creates/updates service in memory
2. Kafka Producer → Publishes message to Kafka topic
3. HTTP Response → Returns immediately to client (service persisted in memory)
4. Kafka Consumer (External) → Persists message to database
5. Indexer → Updates search indices

Benefits: Decoupled write operations, improved throughput, event history

***

### SECTION 7: EXTERNAL SERVICE DEPENDENCIES

#### 7.1 ID Generation Service

| Property  | Value                                             |
| --------- | ------------------------------------------------- |
| Host      | IDGEN\_SERVICE\_HOST                              |
| Endpoint  | /egov-idgen/id/\_generate                         |
| Method    | POST                                              |
| Called By | EnrichmentService.EnrichServiceWithIdGen()        |
| Purpose   | Generate unique service codes with custom formats |

#### 7.2 MDMS Service (Master Data Management)

| Property        | Value                                        |
| --------------- | -------------------------------------------- |
| Host            | MDMS\_SERVICE\_HOST                          |
| Search Endpoint | MDMS\_V2\_SEARCH\_ENDPOINT                   |
| Create Endpoint | MDMS\_V2\_CREATE\_ENDPOINT/{schemaCode}      |
| Update Endpoint | MDMS\_V2\_UPDATE\_ENDPOINT/{schemaCode}      |
| Called By       | All layers extensively                       |
| Purpose         | Configuration, role management, localization |

#### 7.3 Workflow Service

| Property  | Value                                              |
| --------- | -------------------------------------------------- |
| Host      | WORKFLOW\_HOST                                     |
| Endpoint  | WORKFLOW\_SERVICE\_CREATE\_PATH                    |
| Method    | POST                                               |
| Called By | WorkflowService.CreateAndValidateBusinessService() |
| Purpose   | Manage workflow business service configurations    |

#### 7.4 Health Service Request

| Property        | Value                                                  |
| --------------- | ------------------------------------------------------ |
| Host            | HEALTH\_SERVICE\_HOST                                  |
| Create Endpoint | /health-service-request/service/definition/v1/\_create |
| Search Endpoint | /health-service-request/service/definition/v1/\_search |
| Update Endpoint | /health-service-request/service/definition/v1/\_update |
| Called By       | ChecklistService.GetChecklist()                        |
| Purpose         | Manage checklist definitions                           |

***

### SECTION 8: SECURITY & BEST PRACTICES

#### 8.1 Authentication & Authorization

| Aspect         | Implementation                             |
| -------------- | ------------------------------------------ |
| Multi-tenancy  | X-Tenant-Id header on all requests         |
| User Context   | RequestInfo.UserInfo contains user details |
| Search Auth    | Requires auth-token header                 |
| Access Control | Role-based via MDMS role-action mappings   |

#### 8.2 SQL Injection Prevention

| Measure              | Details                                     |
| -------------------- | ------------------------------------------- |
| Parameterization     | All inputs use $1, $2, etc. placeholders    |
| Whitelist Validation | Sort columns validated against allowed list |
| String Validation    | No concatenation of user input into SQL     |

#### 8.3 Data Validation

| Layer      | Validation                                     |
| ---------- | ---------------------------------------------- |
| Controller | JSON payload, header presence, parameter types |
| Service    | Business rules, MDMS schema compliance         |
| Repository | Duplicate checks, audit data validation        |

#### 8.4 Best Practices - Creating Services

1. Always provide complete RequestInfo with user context
2. Ensure module and businessService exist in MDMS
3. Wait for Kafka processing before searching
4. Check for duplicates before creation
5. Handle async delays in service availability

#### 8.5 Best Practices - Searching Services

1. Use pagination for large result sets
2. Apply filters to reduce data volume
3. Use appropriate sort order for use case
4. Cache frequently accessed data
5. Validate limit/offset parameters client-side

#### 8.6 Best Practices - Updating Services

1. Always provide current version number
2. Ensure serviceCode matches between path and payload
3. Understand configuration changes trigger downstream updates
4. Test in non-production first
5. Monitor change processing for errors

#### 8.7 Error Handling Best Practices

1. Retry Logic: Implement exponential backoff for transient failures
2. Monitoring: Watch Kafka consumer lag
3. Validation: Verify MDMS schemas before operations
4. Logging: Log all configuration changes
5. Alerting: Alert on persistent service failures

***

### SECTION 9: DATA STRUCTURES

#### 9.1 Service Object

| Field             | Type    | Required | Description            |
| ----------------- | ------- | -------- | ---------------------- |
| id                | UUID    | Auto     | Service identifier     |
| tenantId          | string  | Yes      | Tenant identifier      |
| module            | string  | Yes      | Module name            |
| businessService   | string  | Yes      | Business service code  |
| status            | string  | Yes      | ACTIVE or INACTIVE     |
| serviceCode       | string  | Auto     | Generated service code |
| additionalDetails | JSON    | No       | Custom data            |
| version           | integer | Auto     | Configuration version  |
| auditDetails      | object  | Auto     | Created/modified info  |

#### 9.2 Search Criteria Object

| Field           | Type    | Optional | Description                           |
| --------------- | ------- | -------- | ------------------------------------- |
| tenantId        | string  | No       | Required filter                       |
| module          | string  | Yes      | Module filter                         |
| businessService | string  | Yes      | Business service filter               |
| serviceCode     | string  | Yes      | Service code filter                   |
| status          | string  | Yes      | Status filter                         |
| offset          | integer | Yes      | Pagination (default: 0)               |
| limit           | integer | Yes      | Pagination (default: 1000, max: 1500) |
| sortBy          | string  | Yes      | Sort field (default: created\_at)     |
| sortOrder       | string  | Yes      | ASC or DESC (default: DESC)           |

#### 9.3 Audit Details Object

| Field            | Type      | Description                       |
| ---------------- | --------- | --------------------------------- |
| createdBy        | UUID      | User who created                  |
| lastModifiedBy   | UUID      | User who last modified            |
| createdTime      | timestamp | Creation timestamp (milliseconds) |
| lastModifiedTime | timestamp | Last modification timestamp       |

***

### SECTION 10: APPENDIX

#### 10.1 Complete API Flow Diagram - Create Service

Client Request (POST /public-service-init/v1/service)

&#x20;   ↓

CreateServiceHandler (Controller)

&#x20;   ├─→ Validate headers and decode JSON

&#x20;   ├─→ Assign tenant from header

&#x20;   │

&#x20;   ├─→ EnrichServiceWithIdGen (Service)

&#x20;   │   ├─→ Get MDMS config

&#x20;   │   ├─→ Generate service code (ID Generation Service)

&#x20;   │   └─→ Create action-test MDMS entries

&#x20;   │

&#x20;   ├─→ Validate (ValidationService)

&#x20;   │   ├─→ IDgen Validation

&#x20;   │   ├─→ Bill Validation

&#x20;   │   ├─→ Workflow Validation

&#x20;   │   └─→ Create 8 types of localization

&#x20;   │

&#x20;   ├─→ GetChecklist (ChecklistService)

&#x20;   │   ├─→ Get MDMS checklist config

&#x20;   │   └─→ Create checklists in Health Service

&#x20;   │

&#x20;   ├─→ SearchMDMS (final config)

&#x20;   │

&#x20;   └─→ CreateService (PublicService → PublicRepository)

&#x20;       ├─→ Duplicate check via SearchService

&#x20;       ├─→ Generate UUID

&#x20;       ├─→ Publish to SAVE\_PUBLIC\_SERVICE topic

&#x20;       ├─→ Publish to SAVE\_SERVICE\_VERSION\_CONFIG\_MAPPING topic

&#x20;       └─→ Return response HTTP 200

\


#### 10.2 Complete API Flow Diagram - Search Service

Client Request (GET /public-service-init/v1/service?filters)

&#x20;   ↓

SearchServiceHandler (Controller)

&#x20;   ├─→ Validate X-Tenant-Id header

&#x20;   ├─→ Parse query parameters

&#x20;   ├─→ Validate offset (≥ 0, numeric)

&#x20;   ├─→ Validate limit (1-1500, default 1000)

&#x20;   │

&#x20;   └─→ SearchService (PublicService → PublicRepository)

&#x20;       ├─→ Build dynamic SQL with base SELECT

&#x20;       ├─→ Add WHERE clauses for filters

&#x20;       ├─→ Validate sort column against whitelist

&#x20;       ├─→ Add ORDER BY clause

&#x20;       ├─→ Add LIMIT and OFFSET

&#x20;       ├─→ Execute parameterized query

&#x20;       ├─→ Process result rows

&#x20;       └─→ Return HTTP 200 with services array

\


#### 10.3 Complete API Flow Diagram - Update Service

Client Request (PUT /public-service-init/v1/service/{serviceCode})

&#x20;   ↓

UpdateServiceHandler (Controller)

&#x20;   ├─→ Extract serviceCode from path

&#x20;   ├─→ Validate headers and decode JSON

&#x20;   ├─→ Validate serviceCode matches between path and body

&#x20;   ├─→ Get MDMS configuration

&#x20;   │

&#x20;   └─→ UpdateService (PublicService)

&#x20;       ├─→ UpdateService (PublicRepository)

&#x20;       │   ├─→ Check service existence

&#x20;       │   ├─→ Increment version

&#x20;       │   ├─→ Update audit details

&#x20;       │   ├─→ Publish to UPDATE\_PUBLIC\_SERVICE topic

&#x20;       │   └─→ Publish version config mapping

&#x20;       │

&#x20;       └─→ CompareServiceConfigs (if status ≠ INACTIVE)

&#x20;           ├─→ GetServiceVersionConfig (get previous version)

&#x20;           ├─→ Compare root-level changes

&#x20;           ├─→ Log detected changes

&#x20;           │

&#x20;           └─→ ProcessConfigurationChanges

&#x20;               ├─→ billChanged

&#x20;               ├─→ workflowChanged

&#x20;               ├─→ notificationChanged

&#x20;               ├─→ roleChanged

&#x20;               ├─→ checklistChanged

&#x20;               ├─→ idgenChanged

&#x20;               ├─→ documentChanged

&#x20;               ├─→ formChanged

&#x20;               ├─→ applicantsChanged

&#x20;               ├─→ enabledChanged

&#x20;               └─→ Return HTTP 200 with updated service (v2+)

\


#### 10.4 Database Schema Overview

\-- Services table

CREATE TABLE service (

&#x20;   id UUID PRIMARY KEY,

&#x20;   tenant\_id VARCHAR(256) NOT NULL,

&#x20;   module VARCHAR(256) NOT NULL,

&#x20;   business\_service VARCHAR(256) NOT NULL,

&#x20;   status VARCHAR(50),

&#x20;   service\_code VARCHAR(256) UNIQUE,

&#x20;   additional\_details JSONB,

&#x20;   createdby UUID,

&#x20;   last\_modifiedby UUID,

&#x20;   created\_at BIGINT,

&#x20;   updated\_at BIGINT,

&#x20;   version INTEGER DEFAULT 1

);

\


\-- Service version config mapping table

CREATE TABLE service\_version\_config\_mapping (

&#x20;   id UUID PRIMARY KEY,

&#x20;   service\_code VARCHAR(256),

&#x20;   version INTEGER,

&#x20;   config JSONB,

&#x20;   created\_at BIGINT,

&#x20;   createdby UUID

);

\


\-- Indexes for performance

CREATE INDEX idx\_service\_tenant ON service(tenant\_id);

CREATE INDEX idx\_service\_code ON service(service\_code);

CREATE INDEX idx\_service\_module ON service(module);

CREATE INDEX idx\_service\_business ON service(business\_service);

\


#### 10.5 Environment Variables Reference

| Variable                        | Purpose                        | Example                                            |
| ------------------------------- | ------------------------------ | -------------------------------------------------- |
| IDGEN\_SERVICE\_HOST            | ID Generation Service URL      | http://idgen-service:8080                          |
| MDMS\_SERVICE\_HOST             | MDMS Service URL               | http://mdms:8080                                   |
| MDMS\_V2\_SEARCH\_ENDPOINT      | MDMS search path               | /mdms-v2/v2/\_search                               |
| MDMS\_V2\_CREATE\_ENDPOINT      | MDMS create path               | /mdms-v2/v2/\_create                               |
| MDMS\_V2\_UPDATE\_ENDPOINT      | MDMS update path               | /mdms-v2/v2/\_update                               |
| WORKFLOW\_HOST                  | Workflow Service URL           | http://workflow:8080                               |
| WORKFLOW\_SERVICE\_CREATE\_PATH | Workflow business service path | /egov-workflow-v2/egov-wf/businessservice/\_create |
| HEALTH\_SERVICE\_HOST           | Health Service URL             | http://health-service:8080                         |
| KAFKA\_BOOTSTRAP\_SERVERS       | Kafka broker address           | kafka:9092                                         |
| SERVICE\_MODULE\_NAME           | Service module in MDMS         | Studio                                             |
| SERVICE\_MASTER\_NAME           | Service master in MDMS         | ServiceConfiguration                               |
| MDMS\_MAPPING                   | JSON map of service types      | {"documents": {...}, "idgen": {...\}}              |
| LOCALIZATION\_SERVICE\_HOST     | Localization Service URL       | http://localization:8080                           |

#### 10.6 Common Response Examples

**Successful Create Response**

{

&#x20; "ResponseInfo": {

&#x20;   "apiId": "org.egov.public-service-init",

&#x20;   "ver": "1.0.0",

&#x20;   "status": "SUCCESSFUL"

&#x20; },

&#x20; "Services": \[

&#x20;   {

&#x20;     "id": "a1b2c3d4-e5f6-47a8-b9c0-d1e2f3a4b5c6",

&#x20;     "tenantId": "pb",

&#x20;     "module": "Studio",

&#x20;     "businessService": "PB\_BIRTH\_REGISTRATION",

&#x20;     "status": "ACTIVE",

&#x20;     "serviceCode": "BR-2025-001",

&#x20;     "additionalDetails": {},

&#x20;     "version": 1,

&#x20;     "auditDetails": {

&#x20;       "createdBy": "user-uuid",

&#x20;       "lastModifiedBy": "user-uuid",

&#x20;       "createdTime": 1705328400000,

&#x20;       "lastModifiedTime": 1705328400000

&#x20;     }

&#x20;   }

&#x20; ]

}

\


**Successful Search Response**

{

&#x20; "ResponseInfo": {

&#x20;   "status": "successful"

&#x20; },

&#x20; "Services": \[

&#x20;   {

&#x20;     "id": "a1b2c3d4-e5f6-47a8-b9c0-d1e2f3a4b5c6",

&#x20;     "tenantId": "pb",

&#x20;     "module": "Studio",

&#x20;     "businessService": "PB\_BIRTH\_REGISTRATION",

&#x20;     "status": "ACTIVE",

&#x20;     "serviceCode": "BR-2025-001",

&#x20;     "additionalDetails": {},

&#x20;     "version": 1,

&#x20;     "auditDetails": {

&#x20;       "createdBy": "user-uuid",

&#x20;       "lastModifiedBy": "user-uuid",

&#x20;       "createdTime": 1705328400000,

&#x20;       "lastModifiedTime": 1705328400000

&#x20;     }

&#x20;   }

&#x20; ]

}

\


**Error Response**

{

&#x20; "Errors": \[

&#x20;   {

&#x20;     "code": "DUPLICATE\_SERVICE",

&#x20;     "message": "Service already exists with same module, business service and tenantId",

&#x20;     "description": "Cannot create duplicate service for tenant pb, module Studio, business service PB\_BIRTH\_REGISTRATION"

&#x20;   }

&#x20; ]

}

\


#### 10.7 Performance Considerations

| Aspect           | Strategy            | Details                                              |
| ---------------- | ------------------- | ---------------------------------------------------- |
| Database Queries | Parameterized       | All inputs use placeholders                          |
| Pagination       | Configurable        | Default 1000, max 1500 records                       |
| Indexing         | Multi-field         | tenant\_id, service\_code, module, business\_service |
| Caching          | Application-level   | Cache MDMS configs in memory                         |
| Async Processing | Kafka               | Decouple writes from HTTP response                   |
| Retry Logic      | Exponential backoff | MDMS reference validation retries                    |

#### 10.8 Monitoring & Observability

**Key Metrics**

| Metric                 | Purpose              | Alert Threshold     |
| ---------------------- | -------------------- | ------------------- |
| API Response Time      | Latency              | > 2 seconds         |
| Kafka Producer Lag     | Message delivery     | > 10 messages       |
| MDMS API Calls         | External dependency  | > 50% error rate    |
| Database Query Time    | Database performance | > 1 second          |
| Service Creation Count | Usage tracking       | N/A (informational) |

**Logging Points**

* Service creation attempt (with service code and tenant)
* Service update attempt (with version info)
* Configuration change detection (with change types)
* MDMS API calls (request and response)
* Kafka publish operations (success/failure)
* External service call failures

**Health Checks**

* Database connectivity and query performance
* Kafka producer availability
* External service availability (MDMS, IDGen, Workflow, Health Service)
* Configuration validity

#### 10.9 Troubleshooting Guide

**Problem: Service Creation Fails with "Service already exists"**

Cause: Attempting to create duplicate service with same tenant, module, businessService

Solution:

1. Check if service already exists: GET /public-service-init/v1/service?module=X\&businessService=Y
2. If exists, use update instead of create
3. If not visible, check if Kafka consumer has persisted it

**Problem: Search Returns No Results**

Cause: Service not yet persisted to database

Solution:

1. Verify service was created (check response)
2. Wait for Kafka consumer processing (typically < 5 seconds)
3. Check Kafka consumer lag
4. Verify tenant ID matches between create and search requests

**Problem: Update Fails with "serviceCode doesn't match"**

Cause: Path serviceCode differs from request body serviceCode

Solution:

1. Verify URL path contains correct service code
2. Verify request body Service.serviceCode matches path
3. Ensure both are identical (case-sensitive)

**Problem: Configuration Changes Not Processing**

Cause: Status is INACTIVE or changes are not detected

Solution:

1. Verify service status is ACTIVE (not INACTIVE)
2. Check change detection logs
3. Verify MDMS config exists
4. Check external service health (Workflow, Health Service)

**Problem: MDMS Reference Validation Fails**

Cause: MDMS data not yet persisted by other consumers

Solution:

1. Implement retry logic (already built in)
2. Monitor Kafka consumer lag for persister
3. Verify MDMS service is running
4. Check network connectivity to MDMS

#### 10.10 Quick Reference - API Summary

| Operation | Method | Endpoint                               | Auth           | Purpose               |
| --------- | ------ | -------------------------------------- | -------------- | --------------------- |
| Create    | POST   | /public-service-init/v1/service        | Tenant         | Create new service    |
| Search    | GET    | /public-service-init/v1/service        | Tenant + Token | Find services         |
| Update    | PUT    | /public-service-init/v1/service/{code} | Tenant         | Modify service config |

####



\
