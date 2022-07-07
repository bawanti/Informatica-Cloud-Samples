# Informatica Cloud (CDI) Jira Integration using REST APIs

This project contains set of re-usable Informatica Cloud (CDI) design assets to demonstarte how CDI REST V2 connection can be used to interact with Jira Cloud. Most of the use-cases can be implemented using out-of-box Jira CDI connector, but this method can be used if some objects or operations aren't avialble in the OOB connector.

This projects demonstrates how Jira Cloud REST APIs can be used to retrieve projects from Jira Cloud along with option for providing filtering conditions.


<!-- TOC -->
- [Informatica Cloud (CDI) Jira Integration using REST APIs](#informatica-cloud-(cdi)-jira-integration-using-rest-apis)
  - [Design Assets](#design-assets)
  - [Download Design Assets](#download-design-assets)
  - [Setup](#setup)
    - [Jira Configurations](#jira-configurations)
    - [Informatica Cloud Configurations](#informatica-cloud-configurations)
  - [Design Details](#design-details)
    - [Mapping: m_jira_getProjects_flatfile](#mapping:-m_jira_getProjects_flatfile)
    - [Mapping Task: m_jira_getProjects_flatfile](#mapping-task:-mct_jira_getProjects_flatfile) 
  [Run Integration](#run-integrations)
  
<!-- /TOC -->

## **Design Assets**

Below are the list of assets, once imported please publish the assets in the same order as in the below table.

| Asset Name                        | Type                          | Description                                                                                                       |
| ----------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------------|
| JiraGetProjects_Swagger.json   | Swagger File             | Used by RESTV2 connector. Copy this file on the secure agent or a network file share and then provide the file path on the RESTV2 connection                                                                    |
| FF_Tgt_Conn   | Flat File Connection                | Flat file connection used to write Jira results                                                         |
| JiraGetProjects       | RESTV2 Connection                       | Rest Connection used to interact with Jira Cloud using APIs                                               |
| m_jira_getProjects_flatfile                  | Mapping                       | Mapping template with the business logic to connect to Jira Cloud and write to the flat file target                                                                    |
| mct_jira_getProjects_flatfiles                 | Mapping Task                       | Mapping Task which can accept the query parameters and execute the integration                                                          |
| SecureAgentGroup            | Runtime Environment                       | Target runtime environment                                                     |                                                  |

![Exported Assets](./images/Exported-Assets.jpg)

## **Download Design Assets**

Use following links to download the designs assets.

1. Swagger File [Click Here to Download](./designs/latest/JiraGetProjects_Swagger.json)
2. Informatica Cloud Design Assets [Click Here to Download](./designs/latest/JiraCloud-CDI-RESTv2.zip)

## **Setup**

### **Jira Configurations**
Jira Cloud no longer supports basic authentication with username & password, instead we need to use either API Token or OAuth.

If you already have an API token or OAuth details skip this section if not use these instrcutions generate API token required.

Create an API token from your Atlassian account:
  1. Log in to https://id.atlassian.com/manage-profile/security/api-tokens
  2. Click Create API token.
  3. From the dialog that appears, enter a memorable and concise Label for your token and click Create.
  4. Click Copy to clipboard, then paste the token to your script, or elsewhere to save:

![Image](./images/New%20APIToken.jpg)

For further details about the Jira Cloud API Token [Click Here](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/)
### **Informatica Cloud Configurations**
Instrcutions to import and setup Informatica Cloud designs.
1. Update the Swagger file to use required Jira Cloud Instance "[jira-instance-name]". For example it will look something like "instance1-jira.atlassian.net" 
2. Login into informatica Cloud and select Cloud Data Integration service and browse to Explore menu.
3. Then click Import and select the downloaded JiraCloud-CDI-RESTv2.zip file
4. Update the target project name if required if not updated assets will be imported into project named "JIRA".
5. **FF_Tgt_Conn**: If you have an existing flat file connection you can map that connection if not it will new connection. Update the directory path on the connection if new connection is created.
6. **JiraGetprojects**: REST V2 connection
7. **SecureAgentGroup**: Pick rge correct runtime environment for you orgaznization
8. Test and import the design assets.

![Screen shot for your reference below](./images/Import%20Assets.jpg)

9. Once successfully imported review JiraGetprojects RESTV2 connection and make sure that path provided for the Swagger file matches the file location where you have the updated swagger file.

## **Design Details**

###  **Mapping**: m_jira_getProjects_flatfile
Mapping Template which can retrieve projects using  Jira Cloud REST APIs. Integration uses paginated get projects API to retrieve the projects. Click this link all the details on the API. [Click Here](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-search-get) 

Source transformation uses REST web-service to get the projects and supports following query parameters.
| Name                  | Description                                                                                           |
| --------------        | ------------------------------------------------------------------------------------------------------|
| action            | Filter results by projects for which the user can: view , browse ,edit. Default is view	                    |
| maxResults     | The maximum number of items to return per page. Default: 50	    |
| orderBy                 | Order the results by a field. Example: category, issueCount ,key, lastIssueUpdatedTime etc.	                            |
| query                | Filter the results using a literal string. Projects with a matching key or name are returned (case insensitive).	                       |
| typeKey                | Orders results by the project type. This parameter accepts a comma-separated list. Valid values are business, service_desk, and software. |

![Mapping Design](./images/Mapping.jpg)

Pagination is handled using a parameter called **startAt**, this is used within Advannced properties in the source transformations. To determine the last API call for pagination Informatica Cloud uses "End of response expression" or "End Page", between the two attributes whichever happens first will stop the next API calls.

If you expect to retrieve huge number of records adjust the "End Page" attribute accordingly so that API calls do not stop before we retrieve all the records. **We need to make sure that "End of response expression" occurs before we hit "End Page"**.

![Advanced Properties](./images/Source_Trans_Advanced.jpg)

###  **Mapping Task**: mct_jira_getProjects_flatfile

Mapping Task can be used to set the query parameter as required and then execute the integration job to retrieve projects from Jira Cloud. Same mapping task can be exceuted multiple times with different set of query parameter values.

![Mapping Task Design](./images/MappingTask.jpg)

## **Run Integration Job**

Open and click edit mapping task and then set the query parameters as required and run the mapping task.

![Mapping Task Configuration](./images/MCT%20Config.jpg)

Save and run the mapping task. You can check results from the "My Jobs" or "Montor" Service.

![Monitor Job Results](./images/Job%20Monitor.jpg)