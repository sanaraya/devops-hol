# Deploy to Azure Kubernetes Service #

Azure Kubernetes Service manages your hosted Kubernetes environment, making it quicker and easier for you to deploy and manage containerized applications. This service also eliminates the burden of ongoing operations and maintenance by provisioning, upgrading, and scaling resources on demand, without taking your applications offline.

In this step-by-step guide, you'll learn how to create a pipeline that continuously builds and deploys your app. Every time you change your code in a repository that contains a Dockerfile, the images are pushed to your Azure Container Registry, and the manifests are then deployed to your Azure Kubernetes Service cluster.

## Prerequisites

*   A GitHub account, where you can create a repository. If you don't have one, you can [create one for free](https://github.com).

*   An Azure DevOps organization. If you don't have one, you can [create one for free](../../get-started/pipelines-sign-up?view=azure-devops). (An Azure DevOps organization is different from your GitHub organization. Give them the same name if you want alignment between them.)

    If your team already has one, then make sure you're an administrator of the Azure DevOps project that you want to use.

*   An Azure account. If you don't have one, you can [create one for free](https://azure.microsoft.com/free/).

    <div class="TIP">

    Tip

    If you're new at this, the easiest way to get started is to use the same email address as the owner of both the Azure Pipelines organization and the Azure subscription.

    </div>

## Get the code

Fork the following repository containing a sample application and a Dockerfile:

    https://github.com/MicrosoftDocs/pipelines-javascript-docker

## Create the Azure resources

Sign in to the [Azure Portal](https://portal.azure.com/), and then select the [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) button in the upper-right corner.

### Create a container registry

    # Create a resource group
    az group create --name myapp-rg --location eastus

    # Create a container registry
    az acr create --resource-group myapp-rg --name myContainerRegistry --sku Basic

    # Create a Kubernetes cluster
    az aks create \
        --resource-group myapp-rg \
        --name myapp \
        --node-count 1 \
        --enable-addons monitoring \
        --generate-ssh-keys

## Sign in to Azure Pipelines

Sign in to [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines). After you sign in, your browser goes to `https://dev.azure.com/my-organization-name` and displays your Azure DevOps dashboard.

Within your selected organization, create a _project_. If you don't have any projects in your organization, you see a **Create a project to get started** screen. Otherwise, select the **Create Project** button in the upper-right corner of the dashboard.

## Create the pipeline

### Connect and select repository

1.  Sign in to your Azure DevOps organization and navigate to your project.

2.  Go to **Pipelines**, and then select **New Pipeline**.

3.  Walk through the steps of the wizard by first selecting **GitHub** as the location of your source code.

    
    ![Select GitHub](https://docs.microsoft.com/en-us/azure/devops/pipelines/media/get-started-yaml/new-pipeline.png?view=azure-devops)

   **NOTE**
    
    If this is not what you see, then [make sure the Multi-stage pipelines experience is turned on](https://docs.microsoft.com/en-us/azure/devops/project/navigation/preview-features?view=azure-devops).


4.  You might be redirected to GitHub to sign in. If so, enter your GitHub credentials.

5.  When the list of repositories appears, select your repository.

6.  You might be redirected to GitHub to install the Azure Pipelines app. If so, select **Approve and install**.

When the **Configure** tab appears, select **Deploy to Azure Kubernetes Service**.

1.  If you are prompted, select the subscription in which you created your registry and cluster.

2.  Select the `myapp` cluster.

3.  For **Namespace**, select **Existing**, and then select **default**.

4.  Select the name of your container registry.

5.  You can leave the image name and the service port set to the defaults.

6.  Set the **Enable Review App for Pull Requests** checkbox for [review app](../../process/environments-kubernetes?view=azure-devops) related configuration to be included in the pipeline YAML auto-generated in subsequent steps.

7.  Select **Validate and configure**.

    As Azure Pipelines creates your pipeline, it:

    *   Creates a _Docker registry service connection_ to enable your pipeline to push images into your container registry.

    *   Creates an _environment_ and a Kubernetes resource within the environment. For an RBAC enabled cluster, the created Kubernetes resource implicitly creates ServiceAccount and RoleBinding objects in the cluster so that the created ServiceAccount can't perform operations outside the chosen namespace.

    *   Generates an _azure-pipelines.yml_ file, which defines your pipeline.

    *   Generates Kubernetes manifest files. These files are generated by hydrating the [deployment.yml](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/templates/resources/k8s/deployment.yml) and [service.yml](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/templates/resources/k8s/service.yml) templates based on selections you made above.

8.  When your new pipeline appears, review the YAML to see what it does. For more information, see [how we build your pipeline](#how) below. When you're ready, select **Save and run**.

9.  The commit that will create your new pipeline appears. You can see the generated files mentioned above. Select **Save and run**.

10.  If you want, change the **Commit message** to something like _Add pipeline to our repository_. When you're ready, select **Save and run** to commit the new pipeline into your repo, and then begin the first run of your new pipeline!

## See the pipeline run, and your app deployed

As your pipeline runs, watch as your build stage, and then your deployment stage, go from blue (running) to green (completed). You can select the stages and jobs to watch your pipeline in action.

After the pipeline run is finished, explore what happened and then go see your app deployed. From the pipeline summary:

1.  Select the **Environments** tab.

2.  Select **View environment**.

3.  Select the instance if your app for the namespace you deployed to. If you stuck to the defaults we mentioned above, then it will be the **myapp** app in the **default** namespace.

4.  Select the **Services** tab.

5.  Select and copy the external IP address to your clipboard.

6.  Open a new browser tab or window and enter <IP address>:8080.

If you're building our sample app, then _Hello world_ appears in your browser.


## How the pipeline is built

When you finished selecting options and then proceeded to validate and configure the pipeline (see above) Azure Pipelines created a pipeline for you, using the _Deploy to Azure Kubernetes Service_ template.

The build stage uses the [Docker task](../../tasks/build/docker?view=azure-devops) to build and push the image to the Azure Container Registry.

    - stage: Build
      displayName: Build stage
      jobs:  
      - job: Build
        displayName: Build job
        pool:
          vmImage: $(vmImageName)
        steps:
        - task: Docker@2
          displayName: Build and push an image to container registry
          inputs:
            command: buildAndPush
            repository: $(imageRepository)
            dockerfile: $(dockerfilePath)
            containerRegistry: $(dockerRegistryServiceConnection)
            tags: |
              $(tag)

        - task: PublishPipelineArtifact@1
          inputs:
            artifactName: 'manifests'
            path: 'manifests'

The deployment job uses the _Kubernetes manifest task_ to create the `imagePullSecret` required by Kubernetes cluster nodes to pull from the Azure Container Registry resource. Manifest files are then used by the Kubernetes manifest task to deploy to the Kubernetes cluster.

    - stage: Deploy
      displayName: Deploy stage
      dependsOn: Build
      jobs:
      - deployment: Deploy
        displayName: Deploy job
        pool:
          vmImage: $(vmImageName)
        environment: 'azooinmyluggagepipelinesjavascriptdocker.aksnamespace'
        strategy:
          runOnce:
            deploy:
              steps:
              - task: DownloadPipelineArtifact@2
                inputs:
                  artifactName: 'manifests'
                  downloadPath: '$(System.ArtifactsDirectory)/manifests'

              - task: KubernetesManifest@0
                displayName: Create imagePullSecret
                inputs:
                  action: createSecret
                  secretName: $(imagePullSecret)
                  namespace: $(k8sNamespace)
                  dockerRegistryEndpoint: $(dockerRegistryServiceConnection)

              - task: KubernetesManifest@0
                displayName: Deploy to Kubernetes cluster
                inputs:
                  action: deploy
                  namespace: $(k8sNamespace)
                  manifests: |
                    $(System.ArtifactsDirectory)/manifests/deployment.yml
                    $(System.ArtifactsDirectory)/manifests/service.yml
                  imagePullSecrets: |
                    $(imagePullSecret)
                  containers: |
                    $(containerRegistry)/$(imageRepository):$(tag)

## Clean up resources

Whenever you're done with the resources you created above, you can use the following command to delete them:

    az group delete --name myapp-rg

Type `y` when prompted.

    az group delete --name MC_myapp-rg_myapp_eastus

Type `y` when prompted.

## Learn more

*   The services:
    *   [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)
    *   [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
*   The template used to create your pipeline: [Deploy to existing Kubernetes cluster template](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/templates/deploy-to-existing-kubernetes-cluster.yml)
*   Some of the tasks used in your pipeline, and how you can customize them:

*   [Docker task](../../tasks/build/docker?view=azure-devops)
*   [Kubernetes manifest task](../../tasks/deploy/kubernetes-manifest?view=azure-devops)

*   Some of the key concepts for this kind of pipeline:
    *   [Environments](../../process/environments?view=azure-devops)
    *   [Deployment jobs](../../process/deployment-jobs?view=azure-devops)
    *   [Stages](../../process/stages?view=azure-devops)
    *   [Docker registry service connections](../../library/service-endpoints?view=azure-devops#sep-docreg) (the method your pipeline uses to connect to the service)
