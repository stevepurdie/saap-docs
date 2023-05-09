# Add Pipeline to Your Application

Now that we have added our first application using Stakater Opinionated GitOps Structure, we can continue by adding pipeline to our application.

SAAP is shipped with all the tools that you need to add a Tekton pipeline to your application. 

### Tekton Pipeline

> Tekton (OpenShift Pipelines) is the new kid on the block in the CI/CD space. It's grown rapidly in popularity as it's Kubernetes Native way of running CI/CD.

Tekton is deployed as an operator in our cluster and allows users to define in YAML Pipeline and Task definitions. <span style="color:blue;">[Tekton Hub](https://hub.tekton.dev/)</span> is a repository for sharing these YAML resources among the community, giving great reusability to standard workflows.

Tekton is made up of number of YAML files each with a different purpose such as `Task` and `Pipeline`. These are then wrapped together in another YAML file called a `PipelineRun` which represents an instance of a `Pipeline` and a `Workspace` to create an instance of a `Pipeline`.

## Deploying the Tekton Objects

> The Tekton pipeline definitions are not stored with the application codebase because we centralize and share a dynamic Pipeline to avoid duplicated code and effort.

### Tekton Pipeline Chart
We will use stakater's `pipeline-charts` Helm chart to deploy the Tekton resources. The chart contains templates for all required Tekton resources such as `pipeline`, `task`, `eventlistener`, `triggers`, etc.

We will fill in the values for these resources and deploy a functioning pipeline with most of the complexity abstracted away using our Tekton pipeline chart.


The above chart contains all necessary resources needed to build and run a Tekton pipeline. Some of the key things to note above are:
* `eventlistener` -  listens to incoming events like a push to a branch.
* `trigger` - the `eventlistener` specifies a trigger which in turn specifies:
    * `interceptor` - it receives data from the event
    * `triggerbinding` - extracts values from the event interceptor
    * `triggertemplate` - defines `pipeline` run resource template in its definition which in turn references the pipeline

  > **Note**: We do not need to define interceptor and trigger templates in every trigger while using stakater Tekton pipeline chart.

* `pipeline` -  this is the pipeline definition, it wires together all the items above (workspaces, tasks & secrets etc) into a useful & reusable set of activities.
* `tasks` - these are the building blocks of Tekton. They are the custom resources that take parameters and run steps on the shell of a provided image. They can produce results and share workspaces with other tasks.

### SAAP pre-configured cluster tasks:

> SAAP is shipped with many ready-to-use Tekton cluster tasks. Let's take a look at some of the tasks that we will be using to construct a basic pipeline.

1. Navigate to the `OpenShift Console` using `Forecastle`. Select `Pipelines` > `Tasks` in sidebar. Select the `ClusterTasks` tab and search `stakater`. Here you will see all the tasks shipped with SAAP.


### Deploying a working pipeline

> It's finally time to get our hands dirty. Let's use the `tekton-pipeline-chart` and the above tasks to create a working pipeline.

Firstly, we will be populating the values file for the Tekton pipeline Chart to create our pipeline.

1. Open up the `<TENANT-NAME>/nordmart-apps-gitops-config` repository we created in previous section.


2. Navigate to `01-TENANT-NAME` >  `01-tekton-pipelines` > `00-build` folder.


3. Inside the `00-build` folder that you just created, add the following `Chart.yaml`

   ```yaml
   apiVersion: v2
   dependencies:
     - name: pipeline-charts
       repository: https://stakater.github.io/stakater-charts  
       version: 3.6.8
   description: Helm chart for Tekton Pipelines
   name: stakater-main-pr-v1
   version: 0.0.1
   ```
> This `Chart.yaml` uses the pipeline chart as a dependency.

4. Now let's fill in the values file for our chart. Create a `values.yaml` in the same folder and add the following values:
   ```yaml
   pipeline-charts:
      name: stakater-main-pr-v1
      workspaces:
      - name: source
        volumeClaimTemplate:
          accessModes: ReadWriteOnce
          resourcesRequestsStorage: 1Gi
      - name: ssh-directory
        secret:
          secretName: mto-console-repo-ssh-creds
      - name: repo-token
        secret:
          secretName: stakater-ab-token

      pipelines:
        finally:
          - defaultTaskName: stakater-set-commit-status-0-0-3
            name: stakater-set-commit-status
            params:
            - name: GIT_SECRET_NAME
              value: stakater-ab-token
          - defaultTaskName: stakater-remove-environment-0-0-1
            when:
            - input: $(tasks.stakater-github-update-cd-repo-0-0-2.status)
              operator: notin
              values: ["Succeeded"]
        tasks:
          - defaultTaskName: stakater-set-commit-status-0-0-3
            params:
            - name: STATE
              value: pending
          - name: GIT_SECRET_NAME
            value: stakater-ab-token
          - defaultTaskName: git-clone
            params:
            - name: url
              value: "git@github.com:stakater-ab/mto-console.git"
            workspaces:
            - name: ssh-directory
              workspace: ssh-directory
          - defaultTaskName: stakater-create-git-tag-0-0-3
          - defaultTaskName: stakater-create-environment-0-0-2
            params:
            - name: IMAGE_TAG
              value: $(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
          - defaultTaskName: stakater-code-linting-0-0-1
          - defaultTaskName: stakater-kube-linting-0-0-1
            runAfter:
            - stakater-create-environment-0-0-2
          - defaultTaskName: stakater-sonarqube-scan-0-0-2
            runAfter:
            - stakater-kube-linting-0-0-1
            - stakater-code-linting-0-0-1
          - defaultTaskName: stakater-build-image-flag-0-0-2
          - defaultTaskName: stakater-buildah-0-0-2
            name: build-and-push
            params:
            - name: IMAGE
            value: $(params.image_registry_url):$(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
            - name: CURRENT_GIT_TAG
            value: $(tasks.stakater-create-git-tag-0-0-3.results.CURRENT_GIT_TAG)
            - name: BUILD_IMAGE
            value: $(tasks.stakater-build-image-flag-0-0-2.results.BUILD_IMAGE)
          - defaultTaskName: stakater-trivy-scan-0-0-1
            params:
            - name: IMAGE
            value: $(params.image_registry_url):$(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
            - name: BUILD_IMAGE
            value: $(tasks.stakater-build-image-flag-0-0-2.results.BUILD_IMAGE)
            runAfter:
            - build-and-push
          - defaultTaskName: stakater-checkov-scan-0-0-2
            params:
              - name: BUILD_IMAGE
                value: $(tasks.stakater-build-image-flag-0-0-2.results.BUILD_IMAGE)
            runAfter:
              - build-and-push
          - defaultTaskName: stakater-comment-on-pr-0-0-2
            params:
              - name: image
                value: >-
                  $(params.image_registry_url):$(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
            runAfter:
              - stakater-trivy-scan-0-0-1
              - stakater-checkov-scan-0-0-2
          - defaultTaskName: stakater-helm-push-0-0-2
            params:
            - name: semVer
              value: $(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
          - defaultTaskName: stakater-github-update-cd-repo-0-0-2
            params:
              - name: CD_REPO_URL
                value: 'git@github.com:stakater-ab/stakater-apps-gitops-prod.git'
              - name: IMAGE_TAG
                value: $(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
          - defaultTaskName: stakater-push-main-tag-0-0-2
            params:
              - name: IMAGE_TAG
                value: $(tasks.stakater-create-git-tag-0-0-3.results.GIT_TAG)
              - name: GITHUB_TOKEN_SECRET
                value: "mto-console-repo-ssh-creds"

      triggertemplate:
        serviceAccountName: stakater-tekton-builder
        pipelineRunNamePrefix: $(tt.params.repoName)-$(tt.params.prnumberBranch)
        pipelineRunPodTemplate:
          tolerations:
            - key: "pipeline"
              operator: "Exists"
              effect: "NoExecute"
      eventlistener:
        serviceAccountName: stakater-tekton-builder
        triggers:
        - name: stakater-pr-cleaner-v2-pullrequest-merge
          create: false
        - name: github-pullrequest-create
          bindings:
          - ref: stakater-pr-v1
          - name: oldcommit
          value: $(body.pull_request.base.sha)
          - name: newcommit
          value: $(body.pull_request.head.sha)
        - name: github-pullrequest-synchronize
          bindings:
          - ref: stakater-pr-v1
          - name: oldcommit
            value: $(body.before)
          - name: newcommit
            value: $(body.after)
        - name: github-push
          bindings:
          - ref: stakater-main-v1
          - name: oldcommit
            value: $(body.before)
          - name: newcommit
            value: $(body.after)
      rbac:
        enabled: false
      serviceAccount:
        name: stakater-tekton-builder
        create: false
   ```

Here we have defined a basic pipeline which clones the repository when it is triggered, builds its image and Helm chart, and finally updates the version of application.


5. Commit the changes and wait for our Tekton pipelines to deploy out in ArgoCD. Head over to ArgoCD and search for Application `<TENANT_NAME>-build-tekton-pipelines`


![sorcerers-build-Tekton-pipelines.png](./images/sorcerers-build-tekton-pipelines.png)

If you open up the application by clicking on it, you should see a similar screen

![sorcerers-build-Tekton-pipelines.png](./images/sorcerers-build-tekton-pipelines2.png)

6. Let's see our pipeline definition in the SAAP console now. Select `<TENANT_NAME>-build` namespace in the console. Now in the `Pipelines` section, click `pipelines`. You should be able to see the pipeline that you just created using the chart.

![pipeline-basic.png](./images/pipeline-basic.png)

With our pipelines definitions synchronized to the cluster (thanks Argo CD 🐙👏) and our codebase forked, we can now add the webhook to GitLab `nordmart-review` and `nordmart-review-ui` projects.

7. Grab the URL we're going to invoke to trigger the pipeline by checking the event listener route in `<TENANT_NAME>-build` project

   ![add-route.png](./images/add-route.png)

8. Once you have the URL, over on GitLab go to `<TENANT_NAME>/stakater-nordmart-review` > `Settings` > `Webhook` to add the webhook:

    * Add the URL we obtained through the last step in the URL box
    * select `Push Events`, leave the branch empty for now
    * Select `Merge request events`
    * select `SSL Verification`
    * Click `Add webhook` button.


![Nordmart-review-webhook-integration.png](images/webhook.png)

9. Repeat the process for `<TENANT_NAME>/stakater-nordmart-review-ui`. Go to `<TENANT_NAME>/stakater-nordmart-review-ui` project and add the webhook there through the same process.

With all these components in place - now it's time to trigger pipeline via webhook by checking in some code for Nordmart review.

10. Let's make a simple change to `stakater-nordmart-review`. Edit `pom.xml` by adding some new lines in the file. Commit directly to the `main` branch.

11. Navigate to the OpenShift Console ...


🪄 Observe Pipeline running by browsing to `OpenShift UI` > `Pipelines` from left pane > `Pipelines` in your `<TENANT_NAME>-build` project:

![pipeline-running.png](images/pipeline-running.png)

![pipeline-running.png](images/pipeline-running-2.png)

12. Open the logs tab and click build-and-push. Remember the **`tag`** and **`image_sha`** for our image that we will later match with pod spec..

    ![build-and-push-details](images/build-and-push-details.png)


13. When the pipeline is finished, Our `<TENANT_NAME>/nordmart-apps-gitops-config` repo is updated with the new Helm chart version that contains the latest application image. Go to your `<TENANT_NAME>/nordmart-apps-gitops-config` and view the latest commits at `01-<TENANT_NAME>/02-stakater-nordmart-review/01-dev/Chart.yaml`

    ![updated-Nordmart-apps-GitOps-config](images/updated-nordmart-apps-gitops-config.png)

> For pushes to `main` branch, your application is automatically updated in the `<TENANT_NAME>-dev` namespace.

14. Open our `<TENANT>-dev-stakater-nordmart-review` ArgoCD application and click refresh so that our changes are applied to the cluster.

![tenant-dev-Nordmart-review](images/tenant-dev-nordmart-review.png)

15. Navigate to `Workloads` > `Pods` in the sidebar in `<TENANT_NAME>-dev` namespace >  Select the `Pod` named `review-*` > select `YAML` and scroll down to `status:` key. You'll see that the **`tag`** and **`sha`** in the `build-and-push` step match.

![pod-image-updated](images/pod-image-updated.png)

#### Checking Dynamic Test Environment

Now, let's test out the dynamic test environment.

1. Go back to `stakater-nordmart-review` and open up a Merge Request this time instead of pushing directly to main.
   This will trigger the same pipeline.

![pipeline-running.png](images/pipeline-running-2.png)

2. Wait for the pipeline to succeed and then head over to `nordmart-apps-gitops-config`.

3. Navigate to  `nordmart-apps-gitops-config > 01-TENANT_NAME >  02-stakater-nordmart-review > 00-preview`. You should be able to see a yaml file for environment resource.

![pr-27](images/pr-27.png)

![pr27-env](images/pr-27-env.png)


4. Now head over to the openshift console and navigate to projects.

5. Once tronador creates the dynamic environment for you, you should be able to see its project listed there. It will start with the prefix `pr-` followed by your pr number.

![pr27-project](images/pr-27-project.png)


6