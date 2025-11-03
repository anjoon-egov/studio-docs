# Public-application Changes

\
\
\
Check This PR to see what all have been changed in StudioConfig schema and data    [https://github.com/egovernments/DIGIT-Studio/pull/78/files](https://github.com/egovernments/DIGIT-Studio/pull/78/files)

\
\
\


APPLICATION CHANGES:

\


1. Changes in MDMS for service init API.
2. &#x20;WORKFLOW\
   &#x20; "workflow": {

&#x20;                  "ACTIVE": \[],

&#x20;                   "INACTIVE": \[],

}

\


This is a added to state which state will make an application active/inactive

&#x20;    B. DOCUMENT

\
documents": \[

&#x20;                  {

&#x20;                      "module": "checklist\_checktest",

&#x20;                      "actions": \[

&#x20;                          {

&#x20;                              "action": "CREATE",

&#x20;                              "assignee": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          },

&#x20;                          {

&#x20;                              "action": "VERIFY\_AND\_FORWARD",

&#x20;                              "assignee": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          },

&#x20;                          {

&#x20;                              "action": "APPROVE",

&#x20;                              "assignee": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          },

&#x20;                          {

&#x20;                              "action": "SENT\_BACK",

&#x20;                              "assignee": {

&#x20;                                  "show": false,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          },

&#x20;                          {

&#x20;                              "action": "EDIT",

&#x20;                              "assignee": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          },

&#x20;                          {

&#x20;                              "action": "SENT\_BACK",

&#x20;                              "assignee": {

&#x20;                                  "show": true,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "comments": {

&#x20;                                  "show": false,

&#x20;                                  "isMandatory": false

&#x20;                              },

&#x20;                              "documents": \[]

&#x20;                          }

&#x20;                      ],

&#x20;                      "bannerLabel": "OBPS\_BANNER",

&#x20;                      "maxSizeInMB": 5,

&#x20;                      "allowedFileTypes": \[

&#x20;                          "pdf",

&#x20;                          "doc",

&#x20;                          "docx",

&#x20;                          "xlsx",

&#x20;                          "xls",

&#x20;                          "jpeg",

&#x20;                          "jpg",

&#x20;                          "png"

&#x20;                      ]

&#x20;                  }

&#x20;              ],

This is changed and it is made the same structure as MDMS Document config as we will not use a separate master for MDMS Document . we will be using the serviceConfig.Document object&#x20;

\


C. CALCULATOR\
Don’t use different calculator logic\
Tekdi Structure:&#x20;

&#x20;                        "calculator2": {

&#x20;       "type": "custom",

&#x20;       "billingSlabs": \[

&#x20;         {

&#x20;           "key": "PCO\_TOTAL",

&#x20;           "value": 2000

&#x20;         }

&#x20;       ]

&#x20;     },

&#x20;     "calculator": {

&#x20;       "type": "api",

&#x20;       "host": "https://djibouti.tekdinext.com",

&#x20;       "method": "post",

&#x20;       "endpoint": "/calculator-service/v1/BPA\_PCO/demand"

&#x20;     },\
\


&#x20;     Use only one either API or Fixed\
&#x20;   If it is using external API use type as api\
&#x20;   If fixed value type is custom

\
\
\
\


D. LOCALIZATION\
Add the module in\
"localization": {

&#x20;                  "modules": \[

&#x20;                      "digit-studio"

&#x20;                  ]

&#x20;              },

\


It is used to create SMS and notification with the given model as of now we are using modules\[0]

\


E. APPLICANT\
"applicant": {

&#x20;                  "types": \[

&#x20;                      "individual",

&#x20;                      "organisation"

&#x20;                  ],

&#x20;                  "config": {

&#x20;                      "systemUser": true,

&#x20;                      "systemRoles": \[

&#x20;                          "CITIZEN"

&#x20;                      ],

&#x20;                      "systemUserType": "CITIZEN"

&#x20;                  },

&#x20;                  "maximum": 3,

&#x20;                  "minimum": 1

&#x20;              },

\


\
&#x20;   This is changed to know first what are allowed types if\
Individual we have to create individual else organization(This integration is pending)\
&#x20; Config here says what is the user type we have to create along with individual . systemUser true States that we have to create user in user service with roles as given in systemRoles and type as given in systemUserType IF CITIZEN Or EMPLOYEE

\
\
F. SEND SMS and EMAIL Notification in the workflow state&#x20;

\


E. Validate The field and value with regex and restriction along with mdms&#x20;

&#x20;   Values

G.Indexer service changes to add data properly with Process Instance in index\
\
H. Application workflow status to fetch from processInstance Status\
\
\
\
\
\
DB Table Chnages:\
1\.   -- Alter table: applicant - drop columns name, mobile\_number, email\_id

\


ALTER TABLE applicant

&#x20;  DROP COLUMN IF EXISTS name,

&#x20;  DROP COLUMN IF EXISTS mobile\_number,

&#x20;  DROP COLUMN IF EXISTS email\_id;

\


\


ALTER TABLE application ADD CONSTRAINT uq\_application\_application\_number UNIQUE (application\_number);

\


\
2\. MDMS Action Test Sequence create\
CREATE SEQUENCE IF NOT EXISTS mdms\_action\_test\_id\_sequence

&#x20;  START WITH 9990

&#x20;  INCREMENT BY 1;

\
3\. Document table  application to document relation\
\
\


CREATE TABLE IF NOT EXISTS application\_document (

id UUID PRIMARY KEY,

application\_number VARCHAR(255) NOT NULL,

document\_type VARCHAR(255),

file\_store\_id VARCHAR(255),

document\_uid VARCHAR(64),

additional\_details JSONB,

createdby VARCHAR(255),

last\_modifiedby VARCHAR(255),

created\_at TIMESTAMP DEFAULT CURRENT\_TIMESTAMP,

updated\_at TIMESTAMP DEFAULT CURRENT\_TIMESTAMP,

CONSTRAINT fk\_application\_number FOREIGN KEY (application\_number) REFERENCES application(application\_number)

ON DELETE CASCADE

);

\


\


This is to support UI to upload document and keep relation&#x20;

\
