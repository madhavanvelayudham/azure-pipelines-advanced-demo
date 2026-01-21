# Azure DevOps Advanced Pipeline Demo

This repo demonstrates a multi-stage Azure DevOps pipeline:
- CI stage: matrix (Node 18 & 20), npm cache, lint, tests, code coverage, publish artifact
- Package stage: build Docker image and push to Azure Container Registry (ACR)
- Deploy stage: deploy container to Azure App Service (Linux) using the image from ACR
- Pipeline is template-driven (pipelines/templates/*) so you can reuse stages

Prerequisites (Azure side)
1. Azure Container Registry (or use a fully qualified container registry name)
2. Azure App Service (Linux) configured for container deployment (or create one)
3. Service connections in Azure DevOps:
   - Azure Resource Manager service connection (for deployments) — name this and set pipeline variable `azureSubscription`
   - Docker/ACR service connection (for Docker task) — set pipeline variable `acrServiceConnection`
4. Pipeline variables (set in pipeline or variable group):
   - containerRegistry: <yourACRLoginServer> (e.g. myacr.azurecr.io)
   - acrServiceConnection: <service-connection-name>
   - azureSubscription: <ARM-service-connection-name>
   - appName: <your-app-service-name>
   - resourceGroup: <your-app-resource-group>
   - imageName: azure-pipelines-demo
   - imageTag: $(Build.BuildId)

How to use
1. Create repository in Azure DevOps or use existing repo.
2. Copy files from this repo to your local machine and push to the repo.
3. In Azure DevOps:
   - Pipelines -> New pipeline -> Azure Repos Git -> select repo -> Existing Azure Pipelines YAML file -> `azure-pipelines.yml`
   - Create the pipeline.
4. Configure pipeline variables and service connections as above.
5. Run pipeline.

Notes and customizations
- To deploy to AKS instead of App Service, replace `pipelines/templates/deploy.yml` with an AKS/Helm template and use kubectl/helm tasks.
- Add approvals to the `staging` environment in Project Settings -> Pipelines -> Environments -> staging -> Approvals and checks.
- For private npm registries, configure authentication in the pipeline (publish pipeline variables / service connections).
- The Docker task assumes the ACR service connection has push rights.

If you want:
- a variant that deploys to AKS using Helm,
- pipeline that runs Terraform for infra provisioning,
- or additional checks (SonarQube, security scans),
tell me which and I’ll extend this repo.