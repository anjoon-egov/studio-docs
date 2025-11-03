# Public-service

### PUBLIC SERVICE - HELM CONFIGURATION

***

### OVERVIEW

Public Service is the main service for managing applications in Digit Studio. It handles application creation, management, and user interactions with integration to workflow, billing, and notifications.

Dependencies: Kafka (messaging), Postgres (database), Workflow Service, Billing Service\
Deployment: Kubernetes via Helm charts using values.yaml files\
Configuration Type: goland  with database migrations

***

### APPLICATION LABELS & BASIC SETUP

Common identifiers for the application across the platform:

| Field           | Value          |
| --------------- | -------------- |
| App Label       | public-service |
| Group           | rainmaker      |
| Namespace       | egov           |
| Ingress Enabled | true           |
| Zuul Enabled    | true           |
| Context         | public-service |
| App Type        | java-spring    |
| Tracing Enabled | true           |

***

### DATABASE MIGRATION SETUP

Configure automatic database schema initialization:

| Setting              | Value                   |
| -------------------- | ----------------------- |
| DB Migration Enabled | true                    |
| Schema Table         | public\_service\_schema |
| Migration Image      | public-service-db       |

Purpose: Automatically creates or updates database schema on container startup.

***

### CONTAINER CONFIGURATION

Set resource allocation and scaling parameters:

| Parameter       | Value          |
| --------------- | -------------- |
| Container Image | public-service |
| Replicas        | 1              |
| Memory Limit    | 512Mi          |
| CPU Limit       | 300m           |

Note: Adjust replicas for high-traffic environments. Increase memory/CPU for better performance.

***

### DATABASE CONFIGURATION VARIABLES

Connection settings for Postgres database:

DB\_HOST          → ConfigMap: egov-config/db-host

DB\_NAME          → ConfigMap: egov-config/db-name

DB\_USER          → Secret: db/username

DB\_PASSWORD      → Secret: db/password

DB\_PORT          → 5432 (default PostgreSQL port)

\


Security Note: Database credentials stored in Kubernetes secrets, not in code.

***

### WORKFLOW SERVICE VARIABLES

Configure integration with workflow engine for process management:

WORKFLOW\_HOST                    → ConfigMap: egov-service-host/egov-workflow-v2

WORKFLOW\_TRANSITION\_PATH         → egov-workflow-v2/egov-wf/process/\_transition

WORKFLOW\_SEARCH\_PATH             → egov-workflow-v2/egov-wf/process/\_search

WF\_BUSINESS\_SERVICE\_CREATE\_URL   → egov-workflow-v2/egov-wf/businessservice/\_create

WF\_BUSINESS\_SERVICE\_SEARCH\_PATH  → egov-workflow-v2/egov-wf/businessservice/\_search

WF\_BUSINESS\_SERVICE\_UPDATE\_PATH  → egov-workflow-v2/egov-wf/businessservice/\_update

\


Purpose: Enable state transitions and process tracking for public services.

***

### KAFKA CONFIGURATION VARIABLES

Set up event messaging and asynchronous processing:

KAFKA\_BOOTSTRAP\_SERVERS         → ConfigMap: egov-config/kafka-brokers

KAFKA\_PAYMENT\_CONSUMER\_ENABLED  → true

\


Purpose: Connect to Kafka broker for event-driven architecture.

***

### INDIVIDUAL SERVICE VARIABLES

Integrate with health individual service for personal data management:

INDIVIDUAL\_SERVICE\_HOST         → ConfigMap: egov-service-host/health-individual

INDIVIDUAL\_CREATE\_ENDPOINT      → health-individual/v1/\_create

INDIVIDUAL\_UPDATE\_ENDPOINT      → health-individual/v1/\_update

INDIVIDUAL\_SEARCH\_ENDPOINT      → health-individual/v1/\_search

\


Purpose: Create, update, and retrieve individual records linked to services.

***

### MDMS (MASTER DATA MANAGEMENT) VARIABLES

Configure connections to Master Data Management service for reference data:

MDMS\_SERVICE\_HOST               → \{{ index .Values "mdms-service-host" \}}

MDMS\_SEARCH\_ENDPOINT            → \{{ index .Values "mdms-search-endpoint" \}}

MDMS\_V2\_SEARCH\_ENDPOINT         → \{{ index .Values "mdms-v2-search-endpoint" \}}

MDMS\_SEARCH\_V2\_ENDPOINT         → \{{ index .Values "mdms-search-v2-endpoint" \}}

MDMS\_V2\_CREATE\_ENDPOINT         → \{{ index .Values "mdms-v2-create-endpoint" \}}

MDMS\_V2\_UPDATE\_ENDPOINT         → \{{ index .Values "mdms-v2-update-endpoint" \}}

\


Purpose: Access, create, and update master data configurations.

***

### BILLING SERVICE VARIABLES

Connect to billing system for payment collection and bill generation:

BILLING\_SERVICE\_HOST            → ConfigMap: egov-service-host/billing-service

DEMAND\_CREATE\_ENDPOINT          → billing-service/demand/\_create

BILL\_FETCH\_ENDPOINT             → billing-service/bill/v2/\_fetchbill

\


Purpose: Generate bills and fetch payment demands for services.

***

### ID GENERATION SERVICE VARIABLES

Enable automatic unique ID generation for records:

IDGEN\_SERVICE\_HOST              → ConfigMap: egov-service-host/egov-idgen

IDGEN\_SERVICE\_GENERATE\_URL      → egov-idgen/id/\_generate

\


Purpose: Create unique identifiers for applications, services, and transactions.

***

### LOCALIZATION SERVICE VARIABLES

Support multi-language and localization features:

LOCALIZATION\_SERVICE\_HOST       → ConfigMap: egov-service-host/egov-localization

LOCALIZATION\_CONTEXT\_PATH       → localization/messages/v1

LOCALIZATION\_SEARCH\_ENDPOINT    → /\_search

LOCALIZATION\_UPSERT\_ENDPOINT    → /\_upsert

\


Purpose: Provide translated messages and locale-specific content.

***

### SERVICE REQUEST VARIABLES

Manage service definitions and request handling:

SERVICE\_REQUEST\_HOST            → ConfigMap: egov-service-host/health-service-request

SERVICE\_REQUEST\_CREATE\_ENDPOINT → health-service-request/service/definition/v1/\_create

SERVICE\_REQUEST\_SEARCH\_ENDPOINT → health-service-request/service/definition/v1/\_search

SERVICE\_REQUEST\_UPDATE\_ENDPOINT → health-service-request/service/definition/v1/\_update

\


Purpose: Create, search, and update service request definitions.

***

### SERVICE CONFIGURATION VARIABLES

Define service naming and module settings:

SERVICE\_MASTER\_NAME             → ServiceConfiguration (Master data name)

SERVICE\_MODULE\_NAME             → Studio (Module identifier)

NOTIFICATION\_LOCALE             → en\_IN (Notification language)

LOCALIZATION\_MODULE             → rainmaker-studio (Localization module prefix)

\


Purpose: Centralize service naming conventions and localization scope.

***

### KAFKA TOPICS CONFIGURATION

Event topics for asynchronous messaging and data processing:

SAVE\_PUBLIC\_SERVICE\_APPLICATION             → save-public-service-application

UPDATE\_PUBLIC\_SERVICE\_APPLICATION           → update-public-service-application

SAVE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC\_INDEXER     → save-public-service-application-indexer

UPDATE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC\_INDEXER   → update-public-service-application-indexer

SEND\_SMS\_TOPIC                              → egov.core.notification.sms

SEND\_EMAIL\_TOPIC                            → egov.core.notification.email

KAFKA\_TOPICS\_PAYMENT\_CREATE\_NAME            → egov.collection.payment-create

UPDATE\_PUBLIC\_SERVICE                       → update-public-service

SAVE\_PUBLIC\_SERVICE                         → save-public-service

SAVE\_PUBLIC\_SERVICE\_PROCESS                 → save-public-service-process

SAVE\_SERVICE\_VERSION\_CONFIG\_MAPPING         → save-service-version-config-mapping

DELETE\_BUSINESS\_SERVICE\_TOPIC               → delete-business-service-topic

\


Purpose: Route events to appropriate consumers for indexing, notifications, and billing.

***

### MASTER DATA MANAGEMENT (MDMS) MAPPING

Define schema codes for different data types and configurations:

MDMS\_MAPPING: {

&#x20; "documents": {"schemaCode": "DigitStudio.DocumentConfig2"},

&#x20; "idgen": {"schemaCode": "common-masters.IdFormat"},

&#x20; "bill": {"schemaCode": "BillingService"}

}

\


TAXHEAD      → TaxHeadMaster

TAXPERIOD    → TaxPeriod

BusinessService → BusinessService

\


Purpose: Map application data types to MDMS schema codes for validation and processing.

***

### PUBLIC SERVICE INIT INTEGRATION

Connect to Public Service Init microservice:

PUBLIC\_SERVICE\_HOST             → ConfigMap: egov-service-host/public-service-init

PUBLIC\_SERVICE\_SEARCH\_ENDPOINT  → public-service-init/v1/service

\


Purpose: Initialize and retrieve service configurations from Public Service Init microservice.

***

\
\


### DEPLOYMENT NOTES

✅ Database migrations run automatically on container startup ✅ Service discovery uses ConfigMap and Secret references for flexibility ✅ Horizontal scaling supported by adjusting replica count ✅ Resource limits can be increased for high-load scenarios ✅ Tracing enabled for monitoring and debugging via distributed tracing systems

\
\
\


### MICROSERVICES SETUP

#### 1. Public Service Init Service

Persister: https://github.com/egovernments/configs/blob/UNIFIED-UAT/egov-persister/public-service-persister.yml\
Indexer: https://github.com/egovernments/configs/blob/UNIFIED-UAT/egov-indexer/public-service-indexer.yml\
Add to environment: [https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/environments/unified-dev.yaml](https://github.com/egovernments/DIGIT-DevOps/blob/unified-env-lts/deploy-as-code/helm/environments/unified-dev.yaml)

Devops: [https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/studio-services/public-service](https://github.com/egovernments/DIGIT-DevOps/tree/unified-env-lts/deploy-as-code/helm/charts/studio-services/public-service)

\
\
