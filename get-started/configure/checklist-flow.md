# Checklist Flow

{% hint style="info" %}
At present, the following checklist field types are supported -\
1\. Radio buttons\
2\. Text input
{% endhint %}

### **View Application Screen -**

1. **Extract Application Data:**

* Retrieve `businessService` and `workflowState` from the application object.

2. **Fetch Checklist:**

* Load the checklist configuration corresponding to the current `workflowState`.
* If the module supports multiple workflows, display all relevant checklists for the current state.

3. **Determine Checklist Status:**

* Use the combination of:
  * accountId (Application ID)
  * serviceRequestId (Checklist ID)
* Check if a response exists for this checklist:
  * If a response exists, show a "View Response" option.
  * If no response exists, show a "Fill Checklist" option.

### Fill Checklist Screen -

* Clicking "Fill Checklist" redirects the user to a form screen.
* The form receives:
  * Application ID
  * Service Request ID
* The form is rendered empty, allowing the user to fill it.
* Upon submission, the response is saved with:
  * accountId (Application ID)
  * serviceRequestId (Checklist ID)
  * clientId (Current User ID)

### **View Checklist Response Screen -**

* Clicking "View Response" redirects the user to a read-only checklist view screen.
* The route includes:
  * Application ID
  * Service Request ID
  * Checklist Response ID\
