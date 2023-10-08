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