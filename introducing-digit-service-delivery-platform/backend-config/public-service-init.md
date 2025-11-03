# Public-service-Init

### STUDIO SERVICE CONFIGURATION GUIDE

***

### OVERVIEW

Public Service Init Service creates services using ServiceConfiguration MDMS data for specific modules and business services. It sets up services, workflows, localizations, roles, checklists, and notifications.

Dependencies: Kafka (messaging) and Postgres (storage)\
Deployment: Kubernetes via Helm charts using values.yaml files\
Repository: https://github.com/egovernments/DIGIT-Studio/tree/public-service/studio-services

***

### HELM/CONFIG CONFIGURATION

#### Application Labels & Basic Setup

| Field                | Value                         |
| -------------------- | ----------------------------- |
| App Label            | public-service-init           |
| Group                | rainmaker                     |
| Namespace            | egov                          |
| Ingress Enabled      | true                          |
| Zuul Enabled         | true                          |
| Context              | public-service-init           |
| App Type             | java-spring                   |
| Tracing Enabled      | true                          |
| DB Migration Enabled | true                          |
| Schema Table         | public\_service\_init\_schema |
| Replicas             | 1                             |
| Memory Limits        | 512Mi                         |
| CPU Limits           | 300m                          |

Configuration Link: https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/charts/studio-services/public-service-init/values.yaml

***

#### Database Configuration Variables

Connect to your database using these environment variables:

| Variable     | Source    | Reference           |
| ------------ | --------- | ------------------- |
| DB\_HOST     | configMap | egov-config/db-host |
| DB\_NAME     | configMap | egov-config/db-name |
| DB\_USER     | secret    | db/username         |
| DB\_PASSWORD | secret    | db/password         |
| DB\_PORT     | value     | 5432                |

***

#### Workflow Service Variables

Configure workflow paths for business process management:

| Variable                            | Path                                              |
| ----------------------------------- | ------------------------------------------------- |
| WORKFLOW\_HOST                      | configMap: egov-service-host/egov-workflow-v2     |
| WORKFLOW\_TRANSITION\_PATH          | egov-workflow-v2/egov-wf/process/\_transition     |
| WORKFLOW\_SEARCH\_PATH              | egov-workflow-v2/egov-wf/process/\_search         |
| WF\_BUSINESS\_SERVICE\_CREATE\_URL  | egov-workflow-v2/egov-wf/businessservice/\_create |
| WF\_BUSINESS\_SERVICE\_SEARCH\_PATH | egov-workflow-v2/egov-wf/businessservice/\_search |
| WF\_BUSINESS\_SERVICE\_UPDATE\_PATH | egov-workflow-v2/egov-wf/businessservice/\_update |

***

#### Kafka Configuration Variables

Enable messaging for events and notifications:

| Variable                          | Value                                |
| --------------------------------- | ------------------------------------ |
| KAFKA\_BOOTSTRAP\_SERVERS         | configMap: egov-config/kafka-brokers |
| KAFKA\_PAYMENT\_CONSUMER\_ENABLED | true                                 |

***

#### Individual Service Variables

Configure health individual service endpoints:

| Variable                     | Endpoint                                       |
| ---------------------------- | ---------------------------------------------- |
| INDIVIDUAL\_SERVICE\_HOST    | configMap: egov-service-host/health-individual |
| INDIVIDUAL\_CREATE\_ENDPOINT | health-individual/v1/\_create                  |
| INDIVIDUAL\_UPDATE\_ENDPOINT | health-individual/v1/\_update                  |
| INDIVIDUAL\_SEARCH\_ENDPOINT | health-individual/v1/\_search                  |

***

#### MDMS (Master Data Management) Service Variables

Connect to MDMS for data lookups:

| Variable                   | Value                                           |
| -------------------------- | ----------------------------------------------- |
| MDMS\_SERVICE\_HOST        | \{{ index .Values "mdms-service-host" \}}       |
| MDMS\_SEARCH\_ENDPOINT     | \{{ index .Values "mdms-search-endpoint" \}}    |
| MDMS\_V2\_SEARCH\_ENDPOINT | \{{ index .Values "mdms-v2-search-endpoint" \}} |
| MDMS\_V2\_CREATE\_ENDPOINT | \{{ index .Values "mdms-v2-create-endpoint" \}} |
| MDMS\_V2\_UPDATE\_ENDPOINT | \{{ index .Values "mdms-v2-update-endpoint" \}} |

***

#### Billing Service Variables

Set up billing and payment collection:

| Variable                 | Endpoint                                     |
| ------------------------ | -------------------------------------------- |
| BILLING\_SERVICE\_HOST   | configMap: egov-service-host/billing-service |
| DEMAND\_CREATE\_ENDPOINT | billing-service/demand/\_create              |
| BILL\_FETCH\_ENDPOINT    | billing-service/bill/v2/\_fetchbill          |

***

#### ID Generation Service Variables

Enable unique ID generation for records:

| Variable                      | Value                                   |
| ----------------------------- | --------------------------------------- |
| IDGEN\_SERVICE\_HOST          | configMap: egov-service-host/egov-idgen |
| IDGEN\_SERVICE\_GENERATE\_URL | egov-idgen/id/\_generate                |

***

#### Localization Service Variables

Configure multi-language support:

| Variable                       | Value                                          |
| ------------------------------ | ---------------------------------------------- |
| LOCALIZATION\_SERVICE\_HOST    | configMap: egov-service-host/egov-localization |
| LOCALIZATION\_CONTEXT\_PATH    | localization/messages/v1                       |
| LOCALIZATION\_SEARCH\_ENDPOINT | /\_search                                      |
| LOCALIZATION\_UPSERT\_ENDPOINT | /\_upsert                                      |

***

#### Service Request Variables

Manage service definitions and requests:

| Variable                           | Endpoint                                              |
| ---------------------------------- | ----------------------------------------------------- |
| SERVICE\_REQUEST\_HOST             | configMap: egov-service-host/health-service-request   |
| SERVICE\_REQUEST\_CREATE\_ENDPOINT | health-service-request/service/definition/v1/\_create |
| SERVICE\_REQUEST\_SEARCH\_ENDPOINT | health-service-request/service/definition/v1/\_search |
| SERVICE\_REQUEST\_UPDATE\_ENDPOINT | health-service-request/service/definition/v1/\_update |

***

#### Kafka Topics Configuration

Event-driven messaging topics:

| Topic Name                                | Purpose                       |
| ----------------------------------------- | ----------------------------- |
| save-public-service-application           | Create service applications   |
| update-public-service-application         | Update service applications   |
| save-public-service-application-indexer   | Index for search optimization |
| update-public-service-application-indexer | Update indexed records        |
| egov.core.notification.sms                | Send SMS notifications        |
| egov.core.notification.email              | Send email notifications      |
| egov.collection.payment-create            | Payment collection events     |
| update-public-service                     | Update public services        |
| save-public-service                       | Create public services        |
| save-public-service-process               | Manage service processes      |
| save-service-version-config-mapping       | Version control for configs   |
| delete-business-service-topic             | Delete business services      |

***

#### Master Data Management (MDMS) Mapping

Define schema codes for different data types:

MDMS\_MAPPING: {

&#x20; "documents": {"schemaCode": "DigitStudio.DocumentConfig2"},

&#x20; "idgen": {"schemaCode": "common-masters.IdFormat"},

&#x20; "bill": {"schemaCode": "BillingService"}

}

\


TAXHEAD: TaxHeadMaster

TAXPERIOD: TaxPeriod

BusinessService: BusinessService

\


***

#### Localization & Service Configuration

Set display names and locales:

| Variable              | Value                |
| --------------------- | -------------------- |
| SERVICE\_MASTER\_NAME | ServiceConfiguration |
| SERVICE\_MODULE\_NAME | Studio               |
| NOTIFICATION\_LOCALE  | en\_IN               |
| LOCALIZATION\_MODULE  | rainmaker-studio     |

***

### MICROSERVICES SETUP

#### 1. Public Service Init Service

Persister: https://github.com/egovernments/configs/blob/UNIFIED-UAT/egov-persister/public-service-persister.yml\
Indexer: https://github.com/egovernments/configs/blob/UNIFIED-UAT/egov-indexer/public-service-indexer.yml\
Add to environment: [https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/environments/unified-dev.yaml](https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/environments/unified-dev.yaml)

Devops file: https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/studio-services/public-service-init

***

#### 2. HRMS Service

Purpose: Employee and HR management system\
Code Repo: https://github.com/egovernments/DIGIT-Common/tree/hrms-boundary/egov-hrms\
DB Image: egovio/egov-hrms-db:hrms-boundary-6b3fa2f (Multi-arch)\
Configuration: https://github.com/egovernments/configs/blob/UNIFIED-UAT/egov-persister/hrms-employee-persister.yml

Add Persister Path: file:///work-dir/configs/egov-persister/hrms-employee-persister.yml

SMS Notification Localization:

{

&#x20; "code": "hrms.employee.create.notification",

&#x20; "message": "Hi $employeename, Welcome to mSeva. Your profile successfully set-up: Username - $username Password - $password Visit: $applink Thank you.\n\nEGOVS",

&#x20; "module": "egov-hrms",

&#x20; "locale": "en\_IN"

},

{

&#x20; "code": "hrms.employee.reactivation.notification",

&#x20; "message": "Dear {Employee Name}, Your profile {Username} activated {date}. OTP: {password}. Change password: {link}\n\nEGOVS",

&#x20; "module": "egov-hrms",

&#x20; "locale": "en\_IN"

}

\


***

#### 3. Health Individual Service

Purpose: Manage individual health records\
Code Repo: https://github.com/egovernments/health-campaign-services/tree/master/health-services/individual\
Indexer: https://github.com/egovernments/configs/blob/UNIFIED-UAT/health/egov-indexer/individual-indexer.yml\
Persister: https://github.com/egovernments/configs/blob/UNIFIED-UAT/health/egov-persister/individual-persister.yml\
Add to environment file (same links as Public Service Init above)

***

#### 4. User Service

Purpose: User authentication and management\
Code Repo: https://github.com/egovernments/Digit-Core/tree/sandbox-log/core-services/egov-user\
Devops Files: https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/core-services/egov-user

***

#### 5. User-OTP Service

Purpose: One-time password generation for authentication\
Code Repo: https://github.com/egovernments/Digit-Core/tree/core-services/user-otp\
Devops Files: https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/core-services/user-otp

***

#### 6. Health Service Request

Purpose: Manage health service requests and definitions\
Code Repo: https://github.com/egovernments/health-campaign-services/tree/multiarch-changes/core-services/service-request\
Build Image: health-service-request:multiarch-changes-afff45b\
Config Updates: https://github.com/egovernments/configs/pull/2818/files & https://github.com/egovernments/configs/pull/2817/files

***

#### 7. Inbox Service

Purpose: Manage user notifications and inbox items\
Code Repo: https://github.com/egovernments/DIGIT-Analytics/tree/inbox-parallel-workflow/inbox\
Build Image: inbox:inbox-parallel-workflow-3ac6ecb\
Devops Files: https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/inbox-v2

***

#### 8. Billing Service

Purpose: Generate bills and manage billing cycles\
Code Repo: https://github.com/egovernments/DIGIT-Common/tree/master/billing-service\
Configuration: https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/charts/common-services/billing-service/values.yaml

***

#### 9. Workflow Service

Purpose: Manage process workflows and state transitions\
Code Repo: https://github.com/egovernments/Digit-Core/tree/parallel-workflows-lazy-injection/core-services/egov-workflow-v2\
Build Image: egov-workflow-v2-db:parallel-workflows-lazy-injection-39552997de-35\
Config Update: https://github.com/egovernments/configs/blob/UNIFIED-DEV/egov-persister/egov-workflow-v2-persister.yml

***

#### 10. Gateway Service

Purpose: API gateway and routing\
Code Repo: https://github.com/egovernments/Digit-Core/tree/gateway-2.9.2/core-services/gateway\
Build Images:

* Multi-arch: gateway:gateway-2.9.2-39e3a96
* Single-arch: gateway:gateway-2.9.2-a916a090e6-40

Note: Includes GET API authentication updates

***

### MDMS CONFIGURATION

#### 1. Inbox Configuration

SchemaCode: INBOX.InboxQueryConfiguration

***

#### 2. Role & Action Setup

Add the following roles for Studio module:

{

&#x20; "code": "STUDIO\_ADMIN",

&#x20; "name": "STUDIO ADMIN",

&#x20; "description": "studio service admin"

},

{

&#x20; "code": "STUDIO\_DESIGNER",

&#x20; "name": "Studio Designer",

&#x20; "description": "studio service designer"

},

{

&#x20; "code": "STUDIO\_CITIZEN",

&#x20; "name": "STUDIO CITIZEN",

&#x20; "description": "studio citizen"

}

\


***

#### 3. Role-Action Mapping

Assign STUDIO\_DESIGNER and STUDIO\_ADMIN roles to these API endpoints:

/localization/messages/v1/\_upsert

/egov-workflow-v2/egov-wf/businessservice/\_create

/egov-workflow-v2/egov-wf/businessservice/\_update

/egov-workflow-v2/egov-wf/businessservice/\_search

/egov-workflow-v2/egov-wf/process/\_transition

/egov-workflow-v2/egov-wf/process/\_search

/inbox/v2/\_search

/boundary-service/boundary-relationships/\_create

/boundary-service/boundary-relationships/\_update

/boundary-service/boundary-relationships/\_search

/boundary-service/boundary-hierarchy-definition/\_create

/boundary-service/boundary-hierarchy-definition/\_search

/health-service-request/service/v1/\_search

/health-service-request/service/definition/v1/\_create

/public-service/v1/service

/billing-service/bill/v2/\_fetchbill

/collection-services/payments/\_create

/health-service-request/service/v1/\_create

/mdms-v2/v2/\_search

/studio-pdf-helper/\_data-config

/studio-pdf-helper/\_format-config

/studio-pdf-helper/\_scanQR

/studio-pdf-helper/\_generateQR

/mdms-v2/v2/\_update/Studio.Checklists

/mdms-v2/v2/\_create/Studio.Checklists

/egov-mdms-service/v2/\_create/Studio.ServiceConfiguration

\


***

#### 4. Studio Config MDMS Data

SchemaCode: Studio.ServiceConfiguration\
API collection will be provided with schema and data setup instructions.

***

### IMPORTANT NOTES

✓ All backbone and core services should be running (see: https://core.digit.org/platform/releases/digit-2.9-lts/service-build-updates)

✓ Verify all configuration files exist in config repository before deployment

✓ Multi-arch builds are available for compatibility across different architectures

\
