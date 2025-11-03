# Public-service Changes

\
\
\


SERVICE CHANGES :

\
\


Create service\
\
1\. Bill Object create in MDMS\
&#x20; "bill": {

&#x20;                  "taxHead": \[

&#x20;                      {

&#x20;                          "code": "applicationFee",

&#x20;                          "name": "applicationFee",

&#x20;                          "order": "2",

&#x20;                          "isDebit": false,

&#x20;                          "service": "TESTBSONE",

&#x20;                          "category": "TAX",

&#x20;                          "isRequired": false,

&#x20;                          "isActualDemand": true

&#x20;                      }

&#x20;                  ],

&#x20;                  "taxPeriod": \[

&#x20;                      {

&#x20;                          "code": "TEST2018",

&#x20;                          "toDate": 1554076799000,

&#x20;                          "service": "TESTBSONE",

&#x20;                          "fromDate": 1522540800000,

&#x20;                          "periodCycle": "ANNUAL",

&#x20;                          "financialYear": "2018-19"

&#x20;                      }

&#x20;                  ],

&#x20;                  "BusinessService": {

&#x20;                      "code": "TESTBSONE",

&#x20;                      "businessService": "TESTBSONE",

&#x20;                      "demandUpdateTime": 86400000,

&#x20;                      "isAdvanceAllowed": false,

&#x20;                      "minAmountPayable": 100,

&#x20;                      "partPaymentAllowed": true,

&#x20;                      "isVoucherCreationEnabled": true,

&#x20;                      "collectionModesNotAllowed": \[

&#x20;                          "DD",

&#x20;                          "OFFLINE\_NEFT",

&#x20;                          "OFFLINE\_RTGS",

&#x20;                          "POSTAL\_ORDER"

&#x20;                      ]

&#x20;                  }

&#x20;              }

Used in billing /demand creation and payment&#x20;

\


2.IDGEN MDMS Data Creation

&#x20;\
"idgen": \[

&#x20;                  {

&#x20;                      "type": "application",

&#x20;                      "format": "checklist\_check-test-app-\[cy:yyyy-MM-dd]-\[SEQ\_PUBLIC\_APPLICATION]",

&#x20;                      "idname": "checklist\_check-test.application.test.applicationapp.id"

&#x20;                  },

&#x20;                  {

&#x20;                      "type": "service",

&#x20;                      "format": "checklist\_check-test-svc-\[cy:yyyy-MM-dd]-\[SEQ\_PUBLIC\_APPLICATION]",

&#x20;                      "idname": "checklist\_check-test.application.test.applicationservice.id"

&#x20;                  }

&#x20;              ]

\


3\. Roles CREATE Roles, Create ACTIONTest and RoleAction MApping in MDMS data\
"access": {

&#x20;                  "roles": {

&#x20;                      "editor": \[

&#x20;                          "CHECKLIST\_CHECK\_TEST\_EDITOR"

&#x20;                      ],

&#x20;                      "viewer": \[

&#x20;                          "CHECKLIST\_CHECK\_TEST\_VIEWER"

&#x20;                      ],

&#x20;                      "creator": \[

&#x20;                          "CHECKLIST\_CHECK\_TEST\_CREATOR"

&#x20;                      ]

&#x20;                  },

&#x20;                  "actions": \[

&#x20;                      {

&#x20;                          "url": "test-services/v1/create"

&#x20;                      }

&#x20;                  ]

&#x20;              }

\


4\. WORKFLOW - Create Workflow in Workflow SERVICE\
\
5\. Create CHECKLIST OBJECT in HEALTH SERVICE\
&#x20; "checklist": \[

&#x20;                  {

&#x20;                      "name": "Aug checklist",

&#x20;                      "state": "START",

&#x20;                      "checklistData": {

&#x20;                          "data": \[

&#x20;                              {

&#x20;                                  "id": "2d4a7b1e-1f2f-4a8a-9672-43396c6c9a1c",

&#x20;                                  "key": 1,

&#x20;                                  "type": {

&#x20;                                      "code": "SingleValueList"

&#x20;                                  },

&#x20;                                  "level": 1,

&#x20;                                  "title": "Please type your question here",

&#x20;                                  "value": null,

&#x20;                                  "options": \[

&#x20;                                      {

&#x20;                                          "id": "0cff9846-03a2-4453-bf0e-200cdda5f390",

&#x20;                                          "key": 1,

&#x20;                                          "label": "Please enter the first option here",

&#x20;                                          "subQuestions": \[],

&#x20;                                          "optionComment": false,

&#x20;                                          "optionDependency": false,

&#x20;                                          "parentQuestionId": "2d4a7b1e-1f2f-4a8a-9672-43396c6c9a1c"

&#x20;                                      },

&#x20;                                      {

&#x20;                                          "id": "5260e241-cb2e-4ff8-a0b2-28f0cf664d26",

&#x20;                                          "key": 2,

&#x20;                                          "label": "Option 2",

&#x20;                                          "subQuestions": \[],

&#x20;                                          "optionDependency": false,

&#x20;                                          "parentQuestionId": "2d4a7b1e-1f2f-4a8a-9672-43396c6c9a1c"

&#x20;                                      }

&#x20;                                  ],

&#x20;                                  "isActive": true,

&#x20;                                  "parentId": null,

&#x20;                                  "isRequired": false,

&#x20;                                  "subQuestions": \[]

&#x20;                              },

&#x20;                              {

&#x20;                                  "id": "9a0d423d-0fc5-42b1-b527-597076833c9c",

&#x20;                                  "key": 2,

&#x20;                                  "type": {

&#x20;                                      "code": "Text"

&#x20;                                  },

&#x20;                                  "level": 1,

&#x20;                                  "regex": null,

&#x20;                                  "title": "Additional details",

&#x20;                                  "value": null,

&#x20;                                  "options": \[

&#x20;                                      {

&#x20;                                          "id": "6e2b357b-fee4-40d6-b2c1-467e04955d0c",

&#x20;                                          "key": 1,

&#x20;                                          "label": "Option 1",

&#x20;                                          "subQuestions": \[],

&#x20;                                          "optionComment": false,

&#x20;                                          "optionDependency": false,

&#x20;                                          "parentQuestionId": "9a0d423d-0fc5-42b1-b527-597076833c9c"

&#x20;                                      }

&#x20;                                  ],

&#x20;                                  "isActive": true,

&#x20;                                  "parentId": null,

&#x20;                                  "isRequired": false,

&#x20;                                  "subQuestions": \[]

&#x20;                              },

&#x20;                              {

&#x20;                                  "id": "23ca20d9-c77f-4164-8222-37578e3596b8",

&#x20;                                  "key": 3,

&#x20;                                  "type": {

&#x20;                                      "code": "SingleValueList"

&#x20;                                  },

&#x20;                                  "level": 1,

&#x20;                                  "title": "Rating",

&#x20;                                  "value": null,

&#x20;                                  "options": \[

&#x20;                                      {

&#x20;                                          "id": "6fcc5d7c-7ba2-47a3-941f-5346d81349c9",

&#x20;                                          "key": 1,

&#x20;                                          "label": "1-5",

&#x20;                                          "subQuestions": \[],

&#x20;                                          "optionComment": false,

&#x20;                                          "optionDependency": false,

&#x20;                                          "parentQuestionId": "23ca20d9-c77f-4164-8222-37578e3596b8"

&#x20;                                      },

&#x20;                                      {

&#x20;                                          "id": "5832272a-cb15-44e0-b791-a64365af50a3",

&#x20;                                          "key": 2,

&#x20;                                          "label": "6-10",

&#x20;                                          "subQuestions": \[],

&#x20;                                          "optionDependency": false,

&#x20;                                          "parentQuestionId": "23ca20d9-c77f-4164-8222-37578e3596b8"

&#x20;                                      }

&#x20;                                  ],

&#x20;                                  "isActive": true,

&#x20;                                  "parentId": null,

&#x20;                                  "isRequired": false,

&#x20;                                  "subQuestions": \[]

&#x20;                              }

&#x20;                          ],

&#x20;                          "name": "Aug checklist",

&#x20;                          "isActive": true,

&#x20;                          "description": "Aug checklist"

&#x20;                      }

&#x20;                  }

]\
\
\
\


\
\
\


6\. LOCALIZATION\
\
&#x20;   A. UI Application FIELD LOCALIZATION

&#x20;   B. WORKFLOW STATE , ACTION   and SUBMIT Screen LOCALIZATION

&#x20;   C. NOTIFICATION LOCALIZATION\
&#x20;   D. Button LOCALIZATION

&#x20;   E. CHECKLIST Question and Options LOcalization\
&#x20;   F. INBOX SCREEN /SEARCH SCREEN LOCALIZATION\
&#x20;   G. HEADING Localization\
Note : as of now we only support english localization if any other language then you have add manually with the code

\


7\. Created DB to store the process of failure or success of subtask in create service API\
&#x20; 1.create workflow\
&#x20; 2\. Create idgen\
&#x20; 3\. Create Bill related mdms\
&#x20; 4 .create Document  -> this will be removed in as it is not required\
\
DB Changes\
1\. CREATE TABLE IF NOT EXISTS public\_service\_process (

&#x20;  id UUID PRIMARY KEY,

&#x20;  process\_name VARCHAR(255) NOT NULL,

&#x20;  business\_service VARCHAR(255) NOT NULL,

&#x20;  module VARCHAR(255) NOT NULL,

&#x20;  createdby VARCHAR(255) NOT NULL,

&#x20;  created\_time TIMESTAMP DEFAULT CURRENT\_TIMESTAMP,

&#x20;  success BOOLEAN NOT NULL,

&#x20;  failureReason JSONB

);

\
\
\


\
