# Design Architecture

## System Architecture Overview

DIGIT Studio leverages a **modular, service-oriented architecture** to enable fast, configurable urban governance solutions. The architecture is designed to:\
✔ Streamline frontend interactions with a unified API layer.\
✔ Optimise backend communication by reducing direct dependencies.\
✔ Ensure extensibility through configuration-driven service creation.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Common Services

### 1. Generic Service (Orchestrator)

**Role:** Central entry point for all frontend interactions.

**Capabilities:**

* Manages request routing, validation, and response aggregation across core services.
* Reduces direct dependencies on multiple backend services, ensuring a simplified API layer.
* Supports dynamic module creation through configuration, enabling flexible service activation.
* Initialises required data for dependent core services to ensure seamless integration.
* Provides a default calculator for simple use cases.

#### Integrates With:

✅ MDMS (Master Data Management System)\
✅ Individual Service\
✅ Workflow Service\
✅ PDF Generation Service\
✅ SMS & Notification Service\
✅ Localisation Service\
✅ File Storage (Filestore) Service\
✅ Boundary Service\
✅ ID Generation Service\
✅ Authorisation Service\
✅ Billing & Payment Service

### 2. Application Service

**Role:** Contains **business logic** and orchestrates workflows per module.

**Capabilities:**

* Provides APIs for frontend interactions and internal service communication.
* Supports modular and extensible service development, minimising code modifications.
* Handles dynamic field management, adapting to specific use cases via configuration.
* Can be extended to support new urban governance modules without additional development.

### 3. Transformer Service (Planned for Future Release)

* **Role:** Data transformation and normalisation layer.

**Capabilities:**

* Pulls and structures data from multiple sources.
* Normalises data formats.
* Adds validated data to the registry.
* Works via **dynamic configuration rules** (no hard-coded logic).
* Offload transformation logic from Orchestrator and App Service.

### 4. Record/Registry Service

**Role:** Persistent storage for approved application data.

**Capabilities:**

* Acts as a persistent data layer, reducing redundant API calls and improving efficiency.
* Once an application is approved, it is stored in the registry as a validated record.
* Supports triggering new transactions based on stored records.
* If an external registry is available, data can be pushed to it.
* If no external registry is required, this service serves as the default registry for the application

### 5. Generic UI

**Role:** Dynamic, schema-driven frontend.

**Capabilities:**

* A configuration-driven UI that dynamically adapts based on service configurations.
* Supports dynamic forms, workflows, and screens, tailored to specific service needs.
* Enables a seamless flow from service application to approval and tracking.

### 6. Console (Planned for Future Release)

**Role:** Low-code/no-code configuration interface.

**Capabilities:**

* A low-code/no-code platform for dynamically configuring and modifying services.
* Enables users to create, initialise, and manage services with minimal development effort.
* Facilitates rapid deployment and customisation of services.
* Should include separate configurators for localisation message handling, workflows, SMS, and MDMS data.
* Currently, the console is focused solely on service enablement. Handling of localisation, workflows, SMS, and MDMS may be integrated in future updates.
* Integration of multiple modules - for example, property being referenced in the business license module.

\
