---
page_type: sample
languages:
- python
products:
- azure
- azure-machine-learning-service
- azure-devops
description: "Code which demonstrates how to set up and operationalize an MLOps flow leveraging Azure Machine Learning and Azure DevOps."
---

# MLOps with Azure ML

MLOps will help you to understand how to build the Continuous Integration and Continuous Delivery pipeline for a ML/AI project. We will be using the Azure DevOps Project for build and release/deployment pipelines along with Azure ML services for model retraining pipeline, model management and operationalization.

![ML lifecycle](/docs/images/ml-lifecycle.png)

This template contains code and pipeline definition for a machine learning project demonstrating how to automate an end to end ML/AI workflow. The build pipelines include DevOps tasks for data sanity test, unit test, model training on different compute targets, model version management, model evaluation/model selection, model deployment as realtime web service, staged deployment to QA/prod and integration testing.

## Prerequisite

- Active Azure subscription
- At least contributor access to Azure subscription

## Getting Started

To deploy this solution in your subscription, follow the manual instructions in the [getting started](docs/getting_started.md) doc

If you have deployed this solution in Azure Pipeline **AND** want to recreate your Azure environment:

1. Execute the infrastructure as code (IaC) pipeline to provision Azure resources
2. Execute the build pipeline to publish AML training pipeline
3. Confirm that release trigger pipeline is executing/executed (from automatic CI trigger). This will trigger AML pipeline to execute, which may take ~ 20 minutes to complete
4. Execute the release deployment pipeline to deploy model to ACI and AKS, which may take ~ 20 minutes to complete

## Architecture Diagram

This reference architecture shows how to implement continuous integration (CI), continuous delivery (CD), and retraining pipeline for an AI application using Azure DevOps and Azure Machine Learning. The solution is built on the scikit-learn diabetes dataset but can be easily adapted for any AI scenario and other popular build systems such as Jenkins and Travis.

![Architecture](/docs/images/main-flow.png)

## Architecture Flow

### Train Model

1. Data Scientist writes/updates the code and push it to git repo. This triggers the Azure DevOps build pipeline (continuous integration).
2. Once the Azure DevOps build pipeline is triggered, it performs code quality checks, data sanity tests, unit tests, builds an [Azure ML Pipeline](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-ml-pipelines) and publishes it in an [Azure ML Service Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace).
3. The [Azure ML Pipeline](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-ml-pipelines) is triggered once the Azure DevOps build pipeline completes. All the tasks in this pipeline runs on Azure ML Compute. Following are the tasks in this pipeline:

    - **Train Model** task executes model training script on Azure ML Compute. It outputs a [model](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#model) file which is stored in the [run history](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#run).

    - **Evaluate Model** task evaluates the performance of the newly trained model with the model in production. If the new model performs better than the production model, the following steps are executed. If not, they will be skipped.

    - **Register Model** task takes the improved model and registers it with the [Azure ML Model registry](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#model-registry). This allows us to version control it.

### Deploy Model

Once you have registered your ML model, you can use Azure ML + Azure DevOps to deploy it.

[Azure DevOps release pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/?view=azure-devops) packages the new model along with the scoring file and its python dependencies into a [docker image](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#image) and pushes it to [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro). This image is used to deploy the model as [web service](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#web-service) across QA and Prod environments. The QA environment is running on top of [Azure Container Instances (ACI)](https://azure.microsoft.com/en-us/services/container-instances/) and the Prod environment is built with [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes).

### Repo Details

You can find the details of the code and scripts in the repository [here](/docs/code_description.md)

### References

- [Azure Machine Learning(Azure ML) Service Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/service/overview-what-is-azure-ml)
- [Azure ML CLI](https://docs.microsoft.com/en-us/azure/machine-learning/service/reference-azure-machine-learning-cli)
- [Azure ML Samples](https://docs.microsoft.com/en-us/azure/machine-learning/service/samples-notebooks)
- [Azure ML Python SDK Quickstart](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/?view=vsts)

---

### PLEASE NOTE FOR THE ENTIRETY OF THIS REPOSITORY AND ALL ASSETS

1. No warranties or guarantees are made or implied.
2. All assets here are provided by me "as is". Use at your own risk. Validate before use.
3. I am not representing my employer with these assets, and my employer assumes no liability whatsoever, and will not provide support, for any use of these assets.
4. Use of the assets in this repo in your Azure environment may or will incur Azure usage and charges. You are completely responsible for monitoring and managing your Azure usage.

---

Unless otherwise noted, all assets here are authored by me. Feel free to examine, learn from, comment, and re-use (subject to the above) as needed and without intellectual property restrictions.

If anything here helps you, attribution and/or a quick note is much appreciated.
