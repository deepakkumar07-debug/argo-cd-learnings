# Argo cd Learnings
Argo cd is one of the GitOps tool



# GitOps

    Infrastructure as code done right, It treats the infrastructure code as Application code. 
    for best practice only CD needs access to deploy IaC in cluster
 * Define infrastructure as code instead of creating it manually.
 * infrastructure can be easily reproduced and replicate using git feature to revert back the changes 
 * infrastructure as code evolved  into defining 
   - infrastructure as code
   - Network as code
   - policy as code
   - configuration as code.
   - security as code.
 these are all the types of definitions as code also known as **X as code**

Example 
Instead of manually creating servers and network and all the around it on aws and 
creating k8s cluster with certain components we define it all of these in certain terraform code
or ansible code and k8s manifest files and bunch of yaml files or other definition files 
that describes our infrastructure platform and its configuration

- in GitOps practice we have separate repository for IaC project and full Devops Pipeline (CI/CD) for it.


# CD Pipeline Push vs Pull model
once code merged into main branch the changes will be automatically applied to the infrastructure through a cd pipeline
In GitOps we have two ways of deployments

**Push Deployment**
Traditionally know from application pipeline on jenkins and gitlab ci/cd etc
application is built pipeline executes a command to deploy a new application version into the environment

**Pull deployment**
Here we have agent installed in envrionment eg k8s cluster that actively pulls the changes from the git repository itself
- the agent will check regularly what is the state of the infrastructure code in the repository and compare it to actual 
state in the environment (i.e) k8s cluster if it sees there is a difference in the repository it will pull and apply these changes
to get the environment from the actual state to the desire state defined in the repository

![pull-deployment](/screenshots/pull-deployment.png)

Examples of Pull based GitOps tool
1. Flux Cd
2. Argo cd
which runs inside the k8s cluster and sync the changes from the git repository to the cluster

## Easy Rollback
- rollback to any previous state easily by git revert to undo
git make single source of truth


# Summarize
Infrastructure code with version control pull requests and CI/CD pipeline
# Overview
1. What is ArgoCD ?
2. Why we need ArgoCD? its use cases
3. How ArgoCD works and Benefits?
5. Simple Project 
    * where we deploy argo cd and setup a fully automated cd pipeline for kubernetes
     configuration changes to get some practical experience with ArgoCd


## CD workflow with Argo Cd
1. Deploy ArgoCd in k8s cluster
2. configure ArgoCD to track git repository
3. ArgoCD monitors for any changes and applies automatically


![cd-workflow](/screenshots/cd-workflow.png)

# Best Practice for Git Repository
It is recommended to keep separate git repository for application source code and applicaiton configuration(k8s manifest file)
![best-practice-for-git-repo](/screenshots/best-practice-for-git-repo.png)

# why separate git repository
if we want to update any application configuration we can only made changes to that repository alone we dont
have to build entire applicaition ci/cd pipeline which includes application frontend and backend code that has not changed
![separate-git-repo](/screenshots/separate-git-repo.png)

![separate-git-repo-1](/screenshots/separate-git-repo-1.png)

so the Jenkins CI pipeline will update the deployment.yaml file in a separate git repository where the k8s manifest files are for the application
ans as soon as configuration file changes in the git repository argocd in cluster will immediately know about it (pull those changes)
so it will pull and apply those changes automatically in the cluster.

![argo-cd flow](/screenshots/argocd-flow.png)


# Argo cd supports k8s manifest files
K8s manifest can be defined in different ways
*  kubernetes yaml file
*  Helm Charts
*  Kustomize.io
* Template files that generates k8s mainfests yaml


Git repository that is tracked and synced by ArgoCD which is GitOps tools sometimes also called git repository,
if image version is upated in deployment yaml file by jenkins or any ci pipeline tool or developer ArgoCD kicks in to apply those changes immediately


![ci-cid](/screenshots/splitting-ci-cd.png)

we can still have an automated ci cd pipeline but with a separation of concerns where different teams are responsible for different parts of that full pipeline




# Installing Argo cd

[ArgoCD Installtion](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)




    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

## Getting ArgoCd Admin ui password

    kubectl get secrets -n argocd argocd-initial-admin-secret -o json | jq -r '.data.password' | base64 --decode


## Configure ArgoCD with "A pplication" CRD

`application.yaml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-argo-application
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/deepakkumar07-debug/argo-cd-learnings.git
    targetRevision: HEAD # always the last commit
    path: dev
  destination:
    server: https://kubernetes.default.svc # internal service name for k8s api server
    namespace: myapp # which namespace argocd will apply the changes from git repo (i.e) apply to myapp namespace

  # if not myapp namespace exists in cluster
  syncPolicy:
    syncOptions:
      - CreateNamespace=true

    #  enable automatic track and sync
    # this automate attribute will configure argoCD to pull the changes in every 3 minutes,
    # if we dont want this dealy, we can configure a Git webhook integration between git repo and argocd.
    automated:
      # enable automatic self-healing
      selfHeal: true # undo or overwrites any manual changes with the git repository state instead.
      # enable automatic pruning, by default, automatic sync will not delete resources(deleting deployment,service)
      prune: true

```

how to synchroinze the above changes, for now we have programmed everything in this yaml. 
only first time we need to apply this application.yaml file manully
for now, if we have ci/cd we dont have to do that also.