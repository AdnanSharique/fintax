<font size="5">  

## **Information Bot**  

</font> 

<font size="3">  

## **1. Create QnA Maker**

</font> 

<font size="4"> 
   Create a QnA Maker Service on Azure Portal and deploy the below template by changing the parameters.

</font> 

   ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "accounts_svcfintaxdemo_qna_name": {
                "defaultValue": "svcfintaxdemo-qna",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.CognitiveServices/accounts",
                "apiVersion": "2021-04-30",
                "name": "[parameters('accounts_svcfintaxdemo_qna_name')]",
                "location": "westus",
                "tags": {
                    "vendor": "dsd"
                },
                "sku": {
                    "name": "S0"
                },
                "kind": "QnAMaker",
                "properties": {
                    "apiProperties": {
                        "qnaRuntimeEndpoint": "https://fintaxdemoapp-qna.azurewebsites.net"
                    },
                    "customSubDomainName": "[parameters('accounts_svcfintaxdemo_qna_name')]",
                    "publicNetworkAccess": "Enabled"
                }
            }
        ]
    }  
   ```

<font size="3">  

## **2. Knowledge Base**

</font> 

<font size="4"> 

We can programmatically migrate a knowledge base from QnA Maker. 
The migration process is programmatically available using the following REST APIs:

  * **Import**  
[Knowledgebase - Create](https://docs.microsoft.com/en-us/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Knowledgebase/Download)  

* **Export**  
[Knowledgebase - Download](https://docs.microsoft.com/en-us/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Knowledgebase/Download)    

</font> 

<font size="3"> 

## **3. Tanslator Service** 

</font> 

<font size="4"> 

Create an azure service and it's ready to use.

</font> 

<font size="3"> 

## **3. Azure Function** 

</font> 

<font size="4">

Create Azure Function using the below template.  

</font> 

   ```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sites_qnamaker_fintax_dev_name": {
            "defaultValue": "qnamaker-fintax-dev",
            "type": "String"
        },
        "serverfarms_ASP_qnamaker_fintax_dev_fd73_externalid": {
            "defaultValue": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/resourceGroups/rg-fintaxdemo-dev/providers/Microsoft.Web/serverfarms/ASP-qnamaker-fintax-dev-fd73",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2021-01-15",
            "name": "[parameters('sites_qnamaker_fintax_dev_name')]",
            "location": "East US",
            "tags": {
                "vendor": "dsd"
            },
            "kind": "functionapp",
            "properties": {
                "enabled": true,
                "hostNameSslStates": [
                    {
                        "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '.azurewebsites.net')]",
                        "sslState": "Disabled",
                        "hostType": "Standard"
                    },
                    {
                        "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '.scm.azurewebsites.net')]",
                        "sslState": "Disabled",
                        "hostType": "Repository"
                    }
                ],
                "serverFarmId": "[parameters('serverfarms_ASP_qnamaker_fintax_dev_fd73_externalid')]",
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "siteConfig": {
                    "numberOfWorkers": 1,
                    "acrUseManagedIdentityCreds": false,
                    "alwaysOn": false,
                    "http20Enabled": false,
                    "functionAppScaleLimit": 200,
                    "minimumElasticInstanceCount": 0
                },
                "scmSiteAlsoStopped": false,
                "clientAffinityEnabled": false,
                "clientCertEnabled": false,
                "clientCertMode": "Required",
                "hostNamesDisabled": false,
                "customDomainVerificationId": "A4AE5B4D8DF5A1171AD5FE6A3AC1C147AA8BE402EDA35DC9661253EEB2078009",
                "containerSize": 1536,
                "dailyMemoryTimeQuota": 0,
                "httpsOnly": false,
                "redundancyMode": "None",
                "storageAccountRequired": false,
                "keyVaultReferenceIdentity": "SystemAssigned"
            }
        },
        {
            "type": "Microsoft.Web/sites/basicPublishingCredentialsPolicies",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/ftp')]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "tags": {
                "vendor": "dsd"
            },
            "properties": {
                "allow": true
            }
        },
        {
            "type": "Microsoft.Web/sites/basicPublishingCredentialsPolicies",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/scm')]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "tags": {
                "vendor": "dsd"
            },
            "properties": {
                "allow": true
            }
        },
        {
            "type": "Microsoft.Web/sites/config",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/web')]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "tags": {
                "vendor": "dsd"
            },
            "properties": {
                "numberOfWorkers": 1,
                "defaultDocuments": [
                    "Default.htm",
                    "Default.html",
                    "Default.asp",
                    "index.htm",
                    "index.html",
                    "iisstart.htm",
                    "default.aspx",
                    "index.php"
                ],
                "netFrameworkVersion": "v4.0",
                "phpVersion": "5.6",
                "requestTracingEnabled": false,
                "remoteDebuggingEnabled": false,
                "httpLoggingEnabled": false,
                "acrUseManagedIdentityCreds": false,
                "logsDirectorySizeLimit": 35,
                "detailedErrorLoggingEnabled": false,
                "publishingUsername": "$qnamaker-fintax-dev",
                "scmType": "None",
                "use32BitWorkerProcess": true,
                "webSocketsEnabled": false,
                "alwaysOn": false,
                "managedPipelineMode": "Integrated",
                "virtualApplications": [
                    {
                        "virtualPath": "/",
                        "physicalPath": "site\\wwwroot",
                        "preloadEnabled": false
                    }
                ],
                "loadBalancing": "LeastRequests",
                "experiments": {
                    "rampUpRules": []
                },
                "autoHealEnabled": false,
                "vnetRouteAllEnabled": false,
                "vnetPrivatePortsCount": 0,
                "localMySqlEnabled": false,
                "ipSecurityRestrictions": [
                    {
                        "ipAddress": "Any",
                        "action": "Allow",
                        "priority": 1,
                        "name": "Allow all",
                        "description": "Allow all access"
                    }
                ],
                "scmIpSecurityRestrictions": [
                    {
                        "ipAddress": "Any",
                        "action": "Allow",
                        "priority": 1,
                        "name": "Allow all",
                        "description": "Allow all access"
                    }
                ],
                "scmIpSecurityRestrictionsUseMain": false,
                "http20Enabled": false,
                "minTlsVersion": "1.2",
                "scmMinTlsVersion": "1.0",
                "ftpsState": "AllAllowed",
                "preWarmedInstanceCount": 0,
                "functionAppScaleLimit": 200,
                "functionsRuntimeScaleMonitoringEnabled": false,
                "minimumElasticInstanceCount": 0,
                "azureStorageAccounts": {}
            }
        },
        {
            "type": "Microsoft.Web/sites/deployments",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/b205f266865c4f1bb40387d72fda4ec5')]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "properties": {
                "status": 4,
                "author_email": "N/A",
                "author": "VS Code",
                "deployer": "ZipDeploy",
                "message": "Created via a push deployment",
                "start_time": "2021-07-19T23:03:27.8313842Z",
                "end_time": "2021-07-19T23:03:33.1924405Z",
                "active": true
            }
        },
        {
            "type": "Microsoft.Web/sites/functions",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/', parameters('sites_qnamaker_fintax_dev_name'))]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "properties": {
                "script_root_path_href": "https://qnamaker-fintax-dev.azurewebsites.net/admin/vfs/site/wwwroot/qnamaker-fintax-dev/",
                "script_href": "https://qnamaker-fintax-dev.azurewebsites.net/admin/vfs/site/wwwroot/qnamaker-fintax-dev/index.js",
                "config_href": "https://qnamaker-fintax-dev.azurewebsites.net/admin/vfs/site/wwwroot/qnamaker-fintax-dev/function.json",
                "test_data_href": "https://qnamaker-fintax-dev.azurewebsites.net/admin/vfs/data/Functions/sampledata/qnamaker-fintax-dev.dat",
                "href": "https://qnamaker-fintax-dev.azurewebsites.net/admin/functions/qnamaker-fintax-dev",
                "config": {},
                "invoke_url_template": "https://qnamaker-fintax-dev.azurewebsites.net/api/qnamaker-fintax-dev",
                "language": "node",
                "isDisabled": false
            }
        },
        {
            "type": "Microsoft.Web/sites/hostNameBindings",
            "apiVersion": "2021-01-15",
            "name": "[concat(parameters('sites_qnamaker_fintax_dev_name'), '/', parameters('sites_qnamaker_fintax_dev_name'), '.azurewebsites.net')]",
            "location": "East US",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', parameters('sites_qnamaker_fintax_dev_name'))]"
            ],
            "properties": {
                "siteName": "qnamaker-fintax-dev",
                "hostNameType": "Verified"
            }
        }
    ]
}
   ```
<font size="4"> 

To deploy the function, following 3 files will be needed.  


**1.  index.js**  

</font> 

<font size="3">

*NOTE: Replace the translator key and endpoint*

</font> 

```javascript
const axios = require('axios')

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var translatedAnswers = [];
    //Get translateToLanguageCode from query
    if (!req.query.translateToLanguageCode || !req.body){
        context.res = {
            status: 400,
            body: "Please pass translateToLanguageCode as query param and QnAAnswer as request body"
        };
    }
    //Get POST json payload
    else
    {
        var translatorKey = "d20c4132e3ca42c2bbc622322c3adb36";
        var translatorEndpoint = "https://api.cognitive.microsofttranslator.com/";
        for await (const answer of req.body.answers) {

            const translateObject = [{Text: answer.answer}]
            const route = `${translatorEndpoint}/translate?api-version=3.0&to=${req.query.translateToLanguageCode}`;

            const response = await axios({
                method: 'post',
                url: route,
                data: translateObject,
                headers: {
                'Content-Type': `application/json`,
                'Ocp-Apim-Subscription-Key':translatorKey
                },
            });
            answer.answer = response.data[0].translations[0].text;
            translatedAnswers.push(answer);
        }
    } 
    context.res = {
        // status: 200, /* Defaults to 200 */
        body: translatedAnswers
    };
}  
```

<font size="4"> 

**2.  function.json**

</font> 

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}

```

<font size="4"> 

**2.  sample.dat**

</font> 

```json
{
  "name": "Azure"
}
```

<font size="3"> 

## **4. Logic App** 

</font> 

<font size="4"> 

Deploy the following template.

</font> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workflows_LogicApp_fintax_dev_name": {
            "defaultValue": "LogicApp-fintax-dev",
            "type": "String"
        },
        "sites_qnamaker_fintax_dev_externalid": {
            "defaultValue": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/resourceGroups/rg-fintaxdemo-dev/providers/Microsoft.Web/sites/qnamaker-fintax-dev",
            "type": "String"
        },
        "connections_cognitiveservicesqnamaker_externalid": {
            "defaultValue": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/resourceGroups/rg-fintaxdemo-dev/providers/Microsoft.Web/connections/cognitiveservicesqnamaker",
            "type": "String"
        },
        "connections_translatorv2_externalid": {
            "defaultValue": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/resourceGroups/rg-fintaxdemo-dev/providers/Microsoft.Web/connections/translatorv2",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Logic/workflows",
            "apiVersion": "2017-07-01",
            "name": "[parameters('workflows_LogicApp_fintax_dev_name')]",
            "location": "eastus",
            "tags": {
                "vendor": "dsd"
            },
            "properties": {
                "state": "Enabled",
                "definition": {
                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "$connections": {
                            "defaultValue": {},
                            "type": "Object"
                        }
                    },
                    "triggers": {
                        "manual": {
                            "type": "Request",
                            "kind": "Http",
                            "inputs": {
                                "schema": {
                                    "Content-Type": "application/json",
                                    "properties": {
                                        "question": {
                                            "type": "string"
                                        }
                                    },
                                    "type": "object"
                                }
                            }
                        }
                    },
                    "actions": {
                        "Detect_language": {
                            "runAfter": {
                                "Initialize_QuestionLanguage": [
                                    "Succeeded"
                                ]
                            },
                            "type": "ApiConnection",
                            "inputs": {
                                "body": {
                                    "Text": "@triggerBody()?['question']"
                                },
                                "host": {
                                    "connection": {
                                        "name": "@parameters('$connections')['translatorv2_1']['connectionId']"
                                    }
                                },
                                "method": "post",
                                "path": "/Detect"
                            }
                        },
                        "Generate_answer": {
                            "runAfter": {
                                "Translate_Question_to_EN": [
                                    "Succeeded"
                                ]
                            },
                            "type": "ApiConnection",
                            "inputs": {
                                "body": {
                                    "question": "@body('Translate_Question_to_EN')",
                                    "top": 1
                                },
                                "headers": {
                                    "EndpointKey": "302d37d9-2992-40b5-a446-6b95ae1d5fb5",
                                    "ServiceHost": "https://fintaxdemoapp-qna.azurewebsites.net/qnamaker"
                                },
                                "host": {
                                    "connection": {
                                        "name": "@parameters('$connections')['cognitiveservicesqnamaker']['connectionId']"
                                    }
                                },
                                "method": "post",
                                "path": "/knowledgebases/@{encodeURIComponent('11e402bc-e359-49d6-b090-edb5e715d9a1')}/generateAnswer"
                            }
                        },
                        "Initialize_QuestionLanguage": {
                            "runAfter": {},
                            "type": "InitializeVariable",
                            "inputs": {
                                "variables": [
                                    {
                                        "name": "QuestionLanguage",
                                        "type": "String"
                                    }
                                ]
                            }
                        },
                        "Parse_initial_answer_to_JSON": {
                            "runAfter": {
                                "Generate_answer": [
                                    "Succeeded"
                                ]
                            },
                            "type": "ParseJson",
                            "inputs": {
                                "content": "@body('Generate_answer')",
                                "schema": {
                                    "properties": {
                                        "activeLearningEnabled": {
                                            "type": "boolean"
                                        },
                                        "answers": {
                                            "items": {
                                                "properties": {
                                                    "answer": {
                                                        "type": "string"
                                                    },
                                                    "id": {
                                                        "type": "integer"
                                                    },
                                                    "isDocumentText": {
                                                        "type": "boolean"
                                                    },
                                                    "metadata": {
                                                        "type": "array"
                                                    },
                                                    "score": {
                                                        "type": [
                                                            "integer",
                                                            "number"
                                                        ]
                                                    }
                                                },
                                                "required": [
                                                    "answer",
                                                    "score",
                                                    "id",
                                                    "isDocumentText",
                                                    "metadata"
                                                ],
                                                "type": "object"
                                            },
                                            "type": "array"
                                        }
                                    },
                                    "type": "object"
                                }
                            }
                        },
                        "Response": {
                            "runAfter": {
                                "qnamaker-fintax-dev": [
                                    "Succeeded"
                                ]
                            },
                            "type": "Response",
                            "kind": "Http",
                            "inputs": {
                                "body": "@body('qnamaker-fintax-dev')",
                                "statusCode": "@outputs('qnamaker-fintax-dev')['statusCode']"
                            }
                        },
                        "Set_QuestionLanguage": {
                            "runAfter": {
                                "Detect_language": [
                                    "Succeeded"
                                ]
                            },
                            "type": "SetVariable",
                            "inputs": {
                                "name": "QuestionLanguage",
                                "value": "@body('Detect_language')?['Code']"
                            }
                        },
                        "Translate_Question_to_EN": {
                            "runAfter": {
                                "Set_QuestionLanguage": [
                                    "Succeeded"
                                ]
                            },
                            "type": "ApiConnection",
                            "inputs": {
                                "body": {
                                    "Text": "@triggerBody()?['question']"
                                },
                                "host": {
                                    "connection": {
                                        "name": "@parameters('$connections')['translatorv2_1']['connectionId']"
                                    }
                                },
                                "method": "post",
                                "path": "/Translate",
                                "queries": {
                                    "to": "en"
                                }
                            }
                        },
                        "qnamaker-fintax-dev": {
                            "runAfter": {
                                "Parse_initial_answer_to_JSON": [
                                    "Succeeded"
                                ]
                            },
                            "type": "Function",
                            "inputs": {
                                "body": "@body('Parse_initial_answer_to_JSON')",
                                "function": {
                                    "id": "[concat(parameters('sites_qnamaker_fintax_dev_externalid'), '/functions/qnamaker-fintax-dev')]"
                                },
                                "queries": {
                                    "translateToLanguageCode": "@variables('QuestionLanguage')"
                                }
                            }
                        }
                    },
                    "outputs": {}
                },
                "parameters": {
                    "$connections": {
                        "value": {
                            "cognitiveservicesqnamaker": {
                                "connectionId": "[parameters('connections_cognitiveservicesqnamaker_externalid')]",
                                "connectionName": "cognitiveservicesqnamaker",
                                "id": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/providers/Microsoft.Web/locations/eastus/managedApis/cognitiveservicesqnamaker"
                            },
                            "translatorv2_1": {
                                "connectionId": "[parameters('connections_translatorv2_externalid')]",
                                "connectionName": "translatorv2",
                                "id": "/subscriptions/3f01ab49-a56f-4ee7-97fa-d23155156b42/providers/Microsoft.Web/locations/eastus/managedApis/translatorv2"
                            }
                        }
                    }
                }
            }
        }
    ]
}
```

<font size="3"> 

## 6. Deploy this web app  

Deploy webapp inside chat_webapp/QnAAssitant

## 7. Deploy the bot service and integrate with web app  

Integrate the bot service with the webapp by using the endpoint that should look like this: https://QnABotWebAppDeploy-fintax.azurewebsites.net/api/messages

