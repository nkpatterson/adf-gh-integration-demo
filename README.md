[![Export and Deploy](https://github.com/US-SouthOU-Demo/adf-gh-integration-demo/actions/workflows/cicd.yml/badge.svg)](https://github.com/US-SouthOU-Demo/adf-gh-integration-demo/actions/workflows/cicd.yml)
# Azure Data Factory + GitHub Integration Demo
This repo contains assets that can be used to demonstrate an integrated CI/CD workflow between Azure Data Factory and GitHub Enterprise. It is based on [this documentation](https://docs.microsoft.com/en-us/azure/data-factory/continuous-integration-deployment-improvements) but, instead of Azure Pipelines, we are using GitHub Actions for automating deployments.

![Workflow Diagram](https://docs.microsoft.com/en-us/azure/data-factory/media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

## Resources
* [CI/CD in Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/continuous-integration-deployment)
* [Automated Publsihing in Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/continuous-integration-deployment-improvements)
* [Pre/Post-Deployment Script Example for ADF Triggers](https://docs.microsoft.com/en-us/azure/data-factory/continuous-integration-deployment#script)
* [Azure Login Action](https://github.com/marketplace/actions/azure-login)
* [Azure Powershell Action](https://github.com/marketplace/actions/azure-powershell-action)
* [Azure ARM Template Deploy Action](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)
* [Create/Upload Release Action](https://github.com/marketplace/actions/create-release)
* [Azure Data Factory Node Package](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities)

## Demo Setup
1. In Azure, create 2 ADF's, 1 for Dev and 1 for Prod 
   1. Skip Git configuration for now
1. Create a service principal using the following script and save the output to a file
   1. az ad sp create-for-rbac --name "{sp-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
1. In GitHub, create a new repo
	 1. Make sure to check the box to create a README
1. In GitHub repo, click Settings > Environments
1. Create the following Environments:
   1. Development
   1. Production
1. In the settings for Development Environment, add the following Environment Secrets
   1. AZURE_CREDENTIALS
	    1. Copy/Paste contents of service principal output
   1. FACTORY_NAME
      1. Specify the development ADF instance name
   1. RESOURCE_GROUP_NAME
      1. Specify the resource group name that contains the development ADF instance
   1. SUBSCRIPTION_ID
      1. Specify the Azure Subscription ID that contains the development ADF instance
1. Repeat these instructions for the Production Environment, changing the credentials/names/ids/etc. for the production ADF instance
1. In the Production Environment, add one or more Required Reviewers
1. In the Production Environment, add a deployment branch rule and set to "main"
1. In Code tab of Repo, click Add file > Create new file
   1. Add the following [file contents](https://raw.githubusercontent.com/US-SouthOU-Demo/adf-gh-integration-demo/main/.github/workflows/validate.yml?token=AACCAMOW6O66FEES3KRRYCLBF7I3S) to ".github/workflows/validate.yml"
1. Add another file
   1. Add the following [file contents](https://raw.githubusercontent.com/US-SouthOU-Demo/adf-gh-integration-demo/main/.github/workflows/cicd.yml?token=AACCAMMJDVKDSXLHIXLRA6LBF7JBK) to ".github/workflows/cicd.yml
1. Add another file
   1. Add the following [file contents](https://raw.githubusercontent.com/US-SouthOU-Demo/adf-gh-integration-demo/main/.github/workflows/scripts/cicd.ps1?token=AACCAMMD4F5JJZJ4ZRH3IC3BF7JDM) to ".github/workflows/scripts/cicd.ps1
1. Add another file
   1. Add the following [file contents](https://raw.githubusercontent.com/US-SouthOU-Demo/adf-gh-integration-demo/main/package.json?token=AACCAMOAWEC67ZATT3NP5EDBGDGGU) to root "package.json"

## Demo Walkthrough
1. In GitHub, create a new Issue and titled "Create a new demo pipeline" and assign to self
1. Open ADF Studio for dev instance
1. Configure the Dev ADF with GitHub SCM integration
1. Go to Author tab and create a new Feature Branch from dropdown
1. Create a new Pipeline
1. Under General, drop a Web action to designer
    1. Set URL to https://api.github.com/zen
    1. Set Method to GET
    1. Add a Header
        1. User-Agent = Awesome-GitHub-Demo
1. Save and Debug to make sure it works
1. Change the branch back to main and observe how there is no pipeline or updates in that branch and go back to new feature branch to see them reappear
1. Create a Pull Request from branch dropdown
1. In body of Pull Request, type "Resolves #" and then select the Issue that was created earlier to link PR to Issue
1. Continue to Open PR 
1. Observe the "Validate ADF Configuration" check firing
1. Click into the Check to view the summary details
1. Click ellipsis and View Workflow File
1. Go over the trigger and what the workflow does (validates the ADF instance specified)
1. In ADF Studio, change the pipeline to be invalid and save
1. Observe the Check failing immediately in the PR
1. Go back to PR and Merge Changes once check passes
1. Go to Actions tab and observe Export and Deploy workflow running
1. Highlight the different environments in the visualization and then go to Settings > Environments to show details
1. Show the logs for each stage and then go to the .github/workflows/cicd.yml file and discuss
1. Observe the deploy pausing after successfully pushing to development
1. Go to ADF Studio and observe that the changes are now in the main branch
1. Go back to GitHub and approve the deploy to Production
1. While waiting to ADF Studio for production instance and observe there is nothing there
1. Wait for deploy to complete and observe changes being applied in ADF Studio
1. In GitHub, show the Release that was created and that the initiating Issue was closed
1. Optional: create another feature branch in dev ADF and update pipeline with changes and watch process end-to-end again
