# Public-service

## PUBLIC SERVICE - GO MICROSERVICE DOCUMENTATION

***

### PROJECT OVERVIEW

| Section      | Details                                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------------------------ |
| Project Name | Public Service                                                                                                     |
| Language     | Go (Golang)                                                                                                        |
| Type         | Microservice                                                                                                       |
| Purpose      | Manages public service applications in DIGIT (Digital Governance and Inclusive Transformation) platform            |
| Key Features | Citizen applications for public services with workflow management, billing/demand generation, multi-tenant support |
| Platform     | DIGIT Ecosystem                                                                                                    |
| Repository   | Public Service (Go-based)                                                                                          |

***

### BUILD & RUN COMMANDS

| Command Type        | Command                                | Purpose                                                   |
| ------------------- | -------------------------------------- | --------------------------------------------------------- |
| Dependencies        | go mod download                        | Install all Go module dependencies                        |
| Build               | go build -o public-service             | Compile the application into executable                   |
| Run Locally         | ./public-service                       | Start the service locally                                 |
| Environment         | Copy and configure .env file           | Set database credentials, service endpoints, Kafka config |
| Database Migrations | FLYWAY\_ENABLED=true                   | Run migrations automatically on startup                   |
| Payment Consumer    | KAFKA\_PAYMENT\_CONSUMER\_ENABLED=true | Enable payment event processing                           |

#### Local Development Setup

* Copy .env file with required configuration
* Service auto-detects Kubernetes environments
* Skips .env loading when running in production (K8s)

#### Database Migrations

* Location: scripts/migration/sql/
* Naming Convention: V{version}\_\_{description}.sql (Flyway format)
* Execution: Automatic on startup when FLYWAY\_ENABLED=true
* Run via: scripts/migration.go

***

### APPLICATION ARCHITECTURE

| Layer      | Directory   | Responsibility                             |
| ---------- | ----------- | ------------------------------------------ |
| Controller | controller/ | HTTP handlers, request/response processing |
| Service    | service/    | Business logic and orchestration           |
| Repository | repository/ | Database operations, external API calls    |
| Model      | model/      | Data structures and domain models          |

#### Architecture Pattern

* Type: Clean Architecture (Layered)
* Separation: Clear concerns across layers
* Flow: Controllers → Services → Repositories → Database/External APIs

***

### &#x20;CORE SERVICES&#x20;

| Service Name       | Purpose                               | Key Methods                               |
| ------------------ | ------------------------------------- | ----------------------------------------- |
| ApplicationService | Core application lifecycle management | Create, Search, Update applications       |
| <p><br></p>        | Coordinates with WorkflowIntegrator   | Manages workflow state                    |
| <p><br></p>        | Enriches with individual user data    | Searches applications with enrichment     |
| <p><br></p>        | <p><br></p>                           | Updates through Kafka async pattern       |
| EnrichmentService  | Data enrichment hub                   | Enrich applications with Individual data  |
| <p><br></p>        | Generates billing/demand              | Generate application numbers (IDGen)      |
| <p><br></p>        | Caches individual data                | Minimize API calls during searches        |
| <p><br></p>        | Supports custom & API billing         | "custom" (slab-based) vs "api" (external) |

***

### &#x20;CORE SERVICES&#x20;

| Service Name       | Purpose                        | Key Methods                                       |
| ------------------ | ------------------------------ | ------------------------------------------------- |
| MDMSV2Service      | Master Data Management         | Fetch configuration schemas from MDMS             |
| <p><br></p>        | Access tenant-specific data    | Search with schema codes                          |
| <p><br></p>        | Configuration source           | Service definitions, workflows, billing slabs     |
| WorkflowIntegrator | Workflow state management      | State transitions via Workflow Service            |
| <p><br></p>        | Process coordination           | Manage workflow actions (submit, approve, reject) |
| <p><br></p>        | Business service configuration | Coordinate with external Workflow Service         |
| SMSService         | Notification handling          | Send SMS and email via Kafka                      |
| <p><br></p>        | Multi-language support         | Use localization service for templates            |
| <p><br></p>        | Triggered on state changes     | Send notifications on workflow transitions        |

***

### &#x20;CORE SERVICES&#x20;

| Service Name          | Purpose                     | Key Methods                                      |
| --------------------- | --------------------------- | ------------------------------------------------ |
| IndividualService     | Citizen profile integration | Fetch individual records from Individual Service |
| <p><br></p>           | Update citizen records      | Create new individual profiles if needed         |
| <p><br></p>           | Data enrichment             | Provide user details for applicant enrichment    |
| DemandService         | Billing and payment         | Create demands via Billing Service               |
| <p><br></p>           | Payment management          | Search existing demands                          |
| <p><br></p>           | Tax calculation             | Dynamic calculation based on MDMS config         |
| ApplicationRepository | Main data access layer      | Publish create/update events to Kafka            |
| <p><br></p>           | Database queries            | Search with complex filtering                    |
| <p><br></p>           | Complex operations          | JOIN applicants and documents tables             |
| <p><br></p>           | <p><br></p>                 | Handle MDMS schema deletion                      |

***

### KAFKA INTEGRATION

| Component   | Topic Name                                | Direction | Purpose                                                   |
| ----------- | ----------------------------------------- | --------- | --------------------------------------------------------- |
| Producer    | save-public-service-application           | OUT       | Publish application create events                         |
| <p><br></p> | update-public-service-application         | OUT       | Publish application updates                               |
| <p><br></p> | save-public-service-application-indexer   | OUT       | Send to search indexer                                    |
| <p><br></p> | update-public-service-application-indexer | OUT       | Update indexed records                                    |
| <p><br></p> | egov.core.notification.sms                | OUT       | SMS notifications                                         |
| <p><br></p> | egov.core.notification.email              | OUT       | Email notifications                                       |
| Consumer    | egov.collection.payment-create            | IN        | Payment events → workflow transitions                     |
| Features    | -                                         | -         | Auto-creates topics if missing                            |
| <p><br></p> | -                                         | -         | Async persistence pattern (events processed by persister) |

***

### MULTI-TENANT DESIGN

| Aspect              | Implementation                | Details                                       |
| ------------------- | ----------------------------- | --------------------------------------------- |
| Data Isolation      | All entities include tenantId | Ensures customer separation                   |
| MDMS Configurations | Tenant-specific lookups       | Different config per customer                 |
| Database Queries    | Filter by tenant              | Queries include WHERE tenantId = X            |
| External Services   | Tenant-aware                  | Individual services respect tenant boundaries |
| Header Requirements | X-Tenant-Id from request      | All APIs require tenant identification        |

***

### API ENDPOINTS - OVERVIEW

| Endpoint                                                | HTTP Method | Purpose                                    | Parameters                                   |
| ------------------------------------------------------- | ----------- | ------------------------------------------ | -------------------------------------------- |
| /public-service/v1/application/{serviceCode}            | POST        | Create application                         | serviceCode in path                          |
| /public-service/v1/application/{serviceCode}            | GET         | Search applications                        | Query: module, businessService, status, etc. |
| /public-service/v1/application/{serviceCode}            | PUT         | Update application object                  | serviceCode in path                          |
| /public-service/v1/application/transition/{serviceCode} | PUT         | Update only workflow                       | serviceCode in path                          |
| /public-service/v1/application/{serviceCode}/count      | GET         | Get  application count based on parameters | serviceCode in path                          |
| /public-service/v1/application/bulk/{serviceCode}       | POST        | Create bulk application                    | serviceCode in path                          |
| /public-service/v1/application                          | GET         | Search own applications                    | Query: userId (current user)                 |
| /public-service/\_calculate(Internal Api)               | POST        | Calculate demand preview                   | ApplicationRequest body                      |

#### Common Headers Required

* X-Tenant-Id - Tenant identifier (all endpoints)
* auth-token - Authentication token (search/update endpoints)

***

### CREATE APPLICATION API FLOW

Endpoint: POST /public-service/v1/application/{serviceCode}

| Step | Component             | Action              | Details                                                  |
| ---- | --------------------- | ------------------- | -------------------------------------------------------- |
| 1    | Controller            | Extract input       | serviceCode from path, X-Tenant-Id from header           |
| 2    | MDMSService           | Validate schema     | Validates application.serviceDetails against JSON schema |
| 3    | EnrichmentService     | Generate App Number | Get idgen config from MDMS                               |
| 4    | IdGenService          | Generate ID         | Call external IDGen service for unique ID                |
| 5    | MDMSV2Service         | Validate applicants | Check applicant types against MDMS allowed types         |
| 6    | IndividualService     | Process applicants  | Search for individuals by mobile, create if missing      |
| 7    | WorkflowIntegrator    | Start workflow      | Call Workflow Service \_transition endpoint              |
| 8    | ApplicationRepository | Publish event       | Send to Kafka: save-public-service-application           |
| 9    | SMSService            | Send SMS            | Get template from Localization, send via Kafka           |
| 10   | SMSService            | Send Email          | Send email notification via Kafka                        |
| 11   | IndexerService        | Send to indexer     | Kafka topic: save-public-service-application-indexer     |
| 12   | Controller            | Return response     | ApplicationResponse with generated app number            |

Key Notes:

* Database persistence happens asynchronously via Kafka
* Individuals created automatically if not found
* Workflow starts immediately
* Notifications sent async

Flowdiadgram:

\


***

### SEARCH APPLICATIONS API FLOW

Endpoint: GET /public-service/v1/application/{serviceCode}

| Step | Component             | Action           | Details                                                          |
| ---- | --------------------- | ---------------- | ---------------------------------------------------------------- |
| 1    | Controller            | Parse filters    | module, businessService, status, applicationNumber, userId, etc. |
| 2    | Controller            | Parse pagination | sortBy, sortOrder, limit (max 1500, default 1000), offset        |
| 3    | Controller            | Validate headers | X-Tenant-Id, auth-token required                                 |
| 4    | ApplicationRepository | Build SQL query  | Dynamic WHERE clauses based on filters                           |
| 5    | ApplicationRepository | Execute query    | JOIN applicants and documents tables                             |
| 6    | ApplicationRepository | Apply sorting    | ORDER BY clause                                                  |
| 7    | ApplicationRepository | Apply pagination | LIMIT and OFFSET                                                 |
| 8    | EnrichmentService     | Enrich data      | For each applicant, fetch Individual details                     |
| 9    | EnrichmentService     | Check cache      | In-memory userCache for already fetched individuals              |
| 10   | IndividualService     | Fetch if missed  | API call only if not in cache                                    |
| 11   | EnrichmentService     | Add fields       | Name, mobile, email, gender, additionalFields                    |
| 12   | Controller            | Return response  | SearchResponse with enriched Application\[]                      |

Key Notes:

* Flexible filtering with multiple criteria
* Pagination with configurable limits
* In-memory caching reduces API calls
* Individual data lazily enriched after database query

***

### UPDATE APPLICATION API FLOW

Endpoint: PUT /public-service/v1/application/{serviceCode}

| Step | Component             | Action               | Details                                                              |
| ---- | --------------------- | -------------------- | -------------------------------------------------------------------- |
| 1    | Controller            | Extract input        | serviceCode, X-Tenant-Id, auth-token                                 |
| 2    | EnrichmentService     | Check demand trigger | Fetch MDMS config for service                                        |
| 3    | EnrichmentService     | Get generateDemandAt | Extract workflow.generateDemandAt\[] array from MDMS                 |
| 4    | EnrichmentService     | Match action         | If application.workflow.action in generateDemandAt → generate demand |
| 5    | MDMSV2Service         | Fetch bill config    | Get BusinessService code and tax periods                             |
| 6    | EnrichmentService     | Calculate demand     | If calculator.type = "custom" use MDMS slabs                         |
| 7    | EnrichmentService     | External calc        | If calculator.type = "api" call external calculator API              |
| 8    | IndividualService     | Get payer info       | Fetch first applicant's Individual details                           |
| 9    | DemandService         | Create demand        | Call Billing Service demand/\_create                                 |
| 10   | WorkflowIntegrator    | Transition workflow  | Call Workflow Service \_transition endpoint                          |
| 11   | ApplicationRepository | Publish update       | Kafka topic: update-public-service-application                       |
| 12   | SMSService            | Send SMS             | Notification via Kafka                                               |
| 13   | SMSService            | Send Email           | Email notification via Kafka                                         |
| 14   | IndexerService        | Update indexer       | Kafka topic: update-public-service-application-indexer               |
| 15   | Controller            | Return response      | ApplicationResponse                                                  |

Key Notes:

* Demand generation conditional on MDMS config
* Two calculator modes: custom (MDMS) or api (external)
* Workflow transition synchronous (must succeed)
* Persistence asynchronous via Kafka
* Failure = entire operation fails

***

### CALCULATE DEMAND API FLOW

Endpoint: POST /public-service/\_calculate

| Step | Component         | Action                        | Details                                                     |
| ---- | ----------------- | ----------------------------- | ----------------------------------------------------------- |
| 1    | EnrichmentService | Fetch config                  | Get service configuration from MDMS                         |
| 2    | EnrichmentService | Get calculator                | Extract calculator section from mdms\[0].data.calculator    |
| 3    | Check type        | Compare calculator.type value | Determines calculation method                               |
| 4a   | If "custom"       | Extract billing slabs         | Get slabs\[] from MDMS: { key: "TAX\_HEAD", value: amount } |
| 4b   | If "custom"       | Build DemandDetail\[]         | Create array with TaxHeadMasterCode and TaxAmount           |
| 5a   | If "api"          | Parse calculator config       | Get host, endpoint, method from MDMS                        |
| 5b   | If "api"          | Make HTTP call                | POST ApplicationRequest to external calculator              |
| 5c   | If "api"          | Parse response                | Extract { demandDetails: \[{ taxhead, amount }] }           |
| 5d   | If "api"          | Build DemandDetail\[]         | Create array from API response                              |
| 6    | Controller        | Return response               | DemandDetail\[] array                                       |

Key Notes:

* Read-only operation (no persistence)
* Dynamic calculator via MDMS
* Can invoke external calculator APIs
* Returns raw DemandDetail\[] without payer info
* Used for "preview charges" in UI

***

### PAYMENT EVENT CONSUMER (BACKGROUND)

Process Type: Background Goroutine

| Step | Component          | Action         | Details                                    |
| ---- | ------------------ | -------------- | ------------------------------------------ |
| 1    | Kafka Consumer     | Listen         | Topic: egov.collection.payment-create      |
| 2    | Parser             | Extract data   | Parse payment message                      |
| 3    | Parser             | Get app number | Extract consumerCode field                 |
| 4    | ApplicationService | Search app     | Find application by application number     |
| 5    | WorkflowIntegrator | Transition     | Trigger workflow state transition          |
| 6    | WorkflowIntegrator | Set action     | Action: "PAY" or configured payment action |
| 7    | SMSService         | Send SMS       | Payment confirmation via Kafka             |
| 8    | SMSService         | Send Email     | Payment confirmation email via Kafka       |

Key Notes:

* Runs continuously as goroutine
* Auto-advances workflow on payment
* Event-driven architecture
* Decouples payment collection from workflow

***

### &#x20;EXTERNAL SERVICE DEPENDENCIES

| Service Name         | Purpose                       | Integration Type | Configuration                                        |
| -------------------- | ----------------------------- | ---------------- | ---------------------------------------------------- |
| Workflow Service     | State machine management      | REST API         | WORKFLOW\_HOST, endpoints                            |
| Individual Service   | Citizen profile management    | REST API         | INDIVIDUAL\_SERVICE\_HOST, endpoints                 |
| Billing Service      | Demand and bill generation    | REST API         | BILLING\_SERVICE\_HOST, endpoints                    |
| MDMS Service         | Master data and configuration | REST API         | MDMS\_SERVICE\_HOST, search/create/update endpoints  |
| IDGen Service        | Unique ID generation          | REST API         | IDGEN\_SERVICE\_HOST, generate URL                   |
| Localization Service | Multi-language messages       | REST API         | LOCALIZATION\_SERVICE\_HOST, search/upsert endpoints |
| Kafka                | Event-driven async processing | Message Broker   | KAFKA\_BOOTSTRAP\_SERVERS                            |

#### Configuration Method

* All URLs configured via environment variables in .env
* RestCallRepository handles all HTTP communication
* Service discovery via ConfigMap references

***

### &#x20;MDMS CONFIGURATION-DRIVEN BEHAVIOR

| Configuration      | Schema Code                 | Usage                         | Fields                           |
| ------------------ | --------------------------- | ----------------------------- | -------------------------------- |
| ID Format          | Studio.ServiceConfiguration | Application number generation | idgen, format, name              |
| Workflow Config    | Studio.ServiceConfiguration | Demand generation triggers    | workflow, generateDemandAt\[]    |
| Billing Calculator | Studio.ServiceConfiguration | Demand calculation method     | calculator, type (custom/api)    |
| Billing Slabs      | Studio.ServiceConfiguration | Tax amounts for custom calc   | billingSlabs, { key, value }     |
| Bill Configuration | Studio.ServiceConfiguration | Business service mapping      | bill, businessService, taxPeriod |
| Document Config    | Studio.ServiceConfiguration | Required documents            | documents, schema, types         |

Key Benefit: Modify service behavior without code changes via MDMS updates

***

### &#x20;DEVELOPMENT GUIDELINES - NEW INTEGRATIONS

| Step | Task                   | Details                                                     |
| ---- | ---------------------- | ----------------------------------------------------------- |
| 1    | Add env variables      | HOST and ENDPOINTS for new service in .env                  |
| 2    | Create service         | New service struct in service/ directory                    |
| 3    | Use RestCallRepository | Follow existing patterns (IndividualService, DemandService) |
| 4    | Wire in main.go        | Add to dependency injection setup                           |
| 5    | Follow patterns        | Mirror existing service integration style                   |

***

### DATABASE CHANGES & MIGRATIONS

| Task             | Steps          | Details                                                 |
| ---------------- | -------------- | ------------------------------------------------------- |
| Create Migration | 1. Create file | Location: scripts/migration/sql/                        |
| <p><br></p>      | 2. Name file   | Format: V{timestamp}\_\_{description}.sql (Flyway)      |
| <p><br></p>      | 3. Write SQL   | Use PostgreSQL syntax (pgx driver, lib/pq)              |
| <p><br></p>      | 4. Deploy      | Runs automatically if FLYWAY\_ENABLED=true              |
| Manual Execution | -              | Migrations auto-run on startup via scripts/migration.go |
| Schema Updates   | All changes    | Via SQL migration files only                            |

***

### &#x20;KAFKA TOPICS REFERENCE

| Topic Name                                | Direction | Purpose                    | Configuration                                        |
| ----------------------------------------- | --------- | -------------------------- | ---------------------------------------------------- |
| save-public-service-application           | OUT       | Create application events  | SAVE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC            |
| update-public-service-application         | OUT       | Update application events  | UPDATE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC          |
| save-public-service-application-indexer   | OUT       | Indexing pipeline (create) | SAVE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC\_INDEXER   |
| update-public-service-application-indexer | OUT       | Indexing pipeline (update) | UPDATE\_PUBLIC\_SERVICE\_APPLICATION\_TOPIC\_INDEXER |
| egov.core.notification.sms                | OUT       | SMS notifications          | SEND\_SMS\_TOPIC                                     |
| egov.core.notification.email              | OUT       | Email notifications        | SEND\_EMAIL\_TOPIC                                   |
| egov.collection.payment-create            | IN        | Payment events (consumed)  | KAFKA\_TOPICS\_PAYMENT\_CREATE\_NAME                 |

***

### WORKING WITH MDMS

| Task               | Method                     | Details                                    |
| ------------------ | -------------------------- | ------------------------------------------ |
| Fetch MDMS Data    | MDMSV2Service.SearchMDMS() | Pass schema code and filters               |
| Schema Code Format | {module}.{masterName}      | Example: Studio.ServiceConfiguration       |
| Filtering          | By service code and module | Gets tenant-specific config                |
| Response Structure | mdms\[0].data.{section}    | Section: idgen, workflow, calculator, etc. |
| Usage Pattern      | Centralized config         | Query once, cache if needed                |

***

### ERROR HANDLING & LOGGING

| Aspect         | Implementation             | Details                               |
| -------------- | -------------------------- | ------------------------------------- |
| Error Wrapping | fmt.Errorf()               | Standard Go error patterns            |
| Logging        | log.Printf() before return | Log before returning errors           |
| JSON Debug     | json.MarshalIndent()       | Pretty-print responses for debugging  |
| Fatal Errors   | DB ping failures           | Service won't start if DB unavailable |
| Recovery       | Graceful degradation       | Handle service failures appropriately |

***

### TESTING

| Aspect            | Status        | Implementation                   |
| ----------------- | ------------- | -------------------------------- |
| Current State     | No tests      | Codebase has no test files yet   |
| Test File Pattern | \*\_test.go   | Create alongside source files    |
| Run Tests         | go test ./... | Run all tests in project         |
| Verbose Output    | go test -v    | For detailed test results        |
| Future Testing    | Add as needed | Follow Go testing best practices |

***

### QUICK REFERENCE - ENVIRONMENT VARIABLES

| Variable                          | Type    | Example                  | Purpose                  |
| --------------------------------- | ------- | ------------------------ | ------------------------ |
| FLYWAY\_ENABLED                   | boolean | true                     | Run DB migrations        |
| KAFKA\_PAYMENT\_CONSUMER\_ENABLED | boolean | true                     | Enable payment events    |
| DB\_HOST                          | string  | localhost                | Database hostname        |
| DB\_PORT                          | integer | 5432                     | Database port            |
| DB\_NAME                          | string  | public\_service          | Database name            |
| DB\_USER                          | string  | user                     | DB username              |
| DB\_PASSWORD                      | string  | password                 | DB password              |
| WORKFLOW\_HOST                    | string  | http://workflow:8080     | Workflow service URL     |
| INDIVIDUAL\_SERVICE\_HOST         | string  | http://individual:8080   | Individual service URL   |
| BILLING\_SERVICE\_HOST            | string  | http://billing:8080      | Billing service URL      |
| MDMS\_SERVICE\_HOST               | string  | http://mdms:8080         | MDMS service URL         |
| KAFKA\_BOOTSTRAP\_SERVERS         | string  | kafka:9092               | Kafka broker address     |
| LOCALIZATION\_SERVICE\_HOST       | string  | http://localization:8080 | Localization service URL |

***

### QUICK REFERENCE - FILE STRUCTURE

| Directory              | Purpose         | Key Files                                   |
| ---------------------- | --------------- | ------------------------------------------- |
| controller/            | HTTP handlers   | Request/response processing                 |
| service/               | Business logic  | ApplicationService, EnrichmentService, etc. |
| repository/            | Data access     | ApplicationRepository, RestCallRepository   |
| model/                 | Data structures | Domain models and DTOs                      |
| kafka/                 | Messaging       | Producer, consumer implementations          |
| scripts/               | Utilities       | Database migration scripts                  |
| scripts/migration/sql/ | Migrations      | Flyway SQL migration files                  |
| .env                   | Configuration   | Local development environment setup         |
| main.go                | Entry point     | Initialization and dependency injection     |

***

End of Documentation

\
