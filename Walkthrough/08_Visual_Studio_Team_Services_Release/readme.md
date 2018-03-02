![Banner](Assets/Banner.png)

0. [Setup](../00_Setup/)
1. [Apps & Organizations](../01_Apps_&_Organizations)
2. [Visual Studio App Center SDK & Analytics](../02_Visual_Studio_App_Center_SDK_&_Analytics)
3. [Build & Distribution](../03_Build_&_Distribution)
4. [Custom Events & Crashes](../04_Custom_Events_&_Crashes)
5. [Automated Testing](../05_Automated_Testing)
6. [Push Notifications](../06_Push_Notifications)
7. [Visual Studio Team Services Build](../07_Visual_Studio_Team_Services_Build)
8. Visual Studio Team Services Release

# Exercise 08: Visual Studio Team Services Release
_Duration: 45 minutes_

In this exercise you will use the Release pipeline of Visual Studio to deploy your backend services to Azure and also your app build to your Visual Studio App Center Distribution Group

## Task1: Prepare Azure Subscription for Deployment

If you haven't already created the Azure backend services then do so now.  While we can do this via a Visual Studio Team Services Release Task, it is likely that the Resource Group you will be deploying to already exists.

Firstly, create a new Resource Group.

![Empty Resource Group](Assets/Azure_Empty_Resource_Group.png)

Now you are ready to create a new Azure Web App which we will use as a target for our Web API deployment.  Click the **Create Resources** button in your empty **Resource Group** and search for **Web App** and click **Create**

![Search for Azure Web App](Assets/Azure_Create_Web_App.png)

Give the **Web App** a name, choose your Azure Subscription, Windows as your OS, turn on Application Insights and choose a location
_Remember, West Europe is in Amsterdam and North Europe is in Dublin_

You will also have to choose an App Service Plan, giving it a name, location and Priciing Tier.
Choose at least a Standard Tier (S1) for the pricing as the free tier does not support deployment slots.

![Create an Azure App Service Plan](Assets/Azure_App_Service_Plan.png)

Now we have an Empty Web App, create a **Staging** slot that we can use for initial deployments.  Click the **Deployment Slots** link in the Web App blade.

![Create a Deployment Slot](Assets/Azure_Create_Slot.png)

## Task2: Associate Azure Subscription with Visual Studio Team Services

Go to the Visual Studio Team Services project **Settings - Services** page

![VSTS Settings Services](Assets/VSTS_Settings_Services.png)

Create a **New Service Endpoint** and choose **Azure Resource Manager**

![Add Service](Assets/VSTS_Add_Azure_Subscription.png)

Now add a memorable Connection Name and choose your subscription.  If you don't see your subscription then use the full dialog and complete the required details.

![Adding an ARM Endpoint](Assets/VSTS_ARM_Endpoint.png)

## Task3: Create a new Visual Studio Team Services Release Definition

Navigate to **Build & Release - Releases** and select **+ New definition**

![VSTS Releases](Assets/VSTS_Ready_For_New_Release.png)

You will be presented with a list of Release Templates.  Choose **Azure App Service Deployment** to deploy the Web API project.

![Release Release Templates](Assets/VSTS_Release_Definition_List.png)

Next you will need to name your first environment.  Call it **Staging** to match the deployment slot we created in Step 1.

![Name release environment](Assets/VSTS_Release_Name_Environment.png)

Click **Add Artifact** and add the Web API build as the Source.

![Add Release artifacts](Assets/VSTS_Release_Add_Artifact.png)

Click **1 phase, 1 task** to view our Staging environment tasks

![View environment tasks](Assets/VSTS_Release_View_Tasks.png)

The template is very simple with only one task, **Azure App Service Deploy**

![App Service Deploy](Assets/VSTS_Release_App_Service_Deploy.png)

Set the following parameters

### Staging Deployment Process

* Azure Subscription = [Your linked ARM Service endpoint]
* App Type = Web App
* App service name = [Select the App Service created in Step 1]

### Azure App Service Deploy

* Deploy to slot = true
* Package or folder = Select ContosoMaintenance.WebAPI.zip from the artifacts dialog

![Add Artifact to release](Assets/VSTS_Release_Deployment_Package.png)

You can optionally specify a Release trigger.  Configure it to trigger in the event of a successful build of the Web API project

![Configure release trigger](Assets/VSTS_Release_Trigger.png)

Manually kick off a new Release

![Create Release](Assets/VSTS_New_Release.png)

You can view the release as it is in progress

![In progress Release](Assets/VSTS_Release_In_Progress.png)

Once the release is complete you can navigate to the Azure portal

![Completed Release](Assets/VSTS_Release_Complete.png)

The Web API is now deployed to the Staging slot of your Azure Web App

![Deployment to staging slot](Assets/Azure_Slot_Deployed.png)

## [Optional] Task4: Create a new Environment

Create a new Environment in your Release Definition called **Live**

Configure an approval and greenlighting process between staging and live environments

Configure Live Environment to swap slots once it has been approved.

Include app deployment to the App Center Distribution Group created in Exercise 3.
