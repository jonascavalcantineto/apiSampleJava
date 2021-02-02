# Github Pipeline Solution

Following the *Devops CAML* principle *(Culture, Automation, LeanIT, Measurement and Sharing)*

**CULTURE**

Culture aims to address the interpersonal relationship of teams.

**AUTOMATION**

Pipeline is the word of the moment, however any other automations that assist in this process are also welcome. The automation of a software delivery flow must be built with great care and attention, so that there are no gaps, nor that it allows the software to be presented with defects.

**LEAN IT**

With the bottlenecks identified, we can optimize the flow, delivering more speed and greater efficiency.

**MEASUREMENT**

Generate feedback on what actually happens with the software.

**SHARING**

The sharing of organizational information helps in the decentralization of knowledge in people of the teams, preventing the processes from becoming dependent.

Solutions used and planned for implementation

**SCM**

Github (Used)

**Build**

Docker (Used)

**Tests**

Sonarqube (Code tests) (Implementation)

Nexus (Libs Repository and Docker Registry) (Implementation)

**Pipeline**

Gitlab CI (Used)

Jenkins (Used)

**Container Orchestration**

Kubernetes (Used)

**Cloud**

AWS (Used)

**Observability**

Prometheus (CPU metrics x Memory X Disk collector)

Grafana (CPU metrics UI x Memory X Disk)

ELK (Logs Management of all containers in the environment)


**Providently from Infrastructure**

Terraform (AWS Resource Generation) (Implementation)

We will explain how CICD is carried out through Gihub. Github will use the pipeline with Jenkins. Follows drawing bellow the flow:
### Architecture

![GitHub Pipeline](https://github.com/jonascavalcantineto/apiSampleJava/blob/master/docs/github/GitHub-CICD-Pipeline.png)

**SCM (Source Code Manager)**

The developer will follow a stream of branchs with the following names: hotfix, feature, qa, production and master

**Hotfix:** Branch to resolve a system bug

**Feature:** Branch for new feature implementations

**QA:** It will be the branch for testing and generating the system version. She does not need confirmation from someone responsible
Production: Branch that will be responsible for generating the deployment in the production environment. In this phase, a QA version tag is made and deployed to a container orchestrator.

**Master:** Branch for generating the realease version of the system in TAG format

**Anatomy of Hotfix and Feature branches**

hotfix / <user email> / <task number>

feature / <user email> / <task number>

**Github - Realization of the pipeline through the Jenkinsfile file**

1. The following stages will be held

    - Build

    System package construction

    - test

     Conducting code tests

    - Release Image

     Construction of the container system image

    - Deploy
    
     Deploy in an orchestrated Kubernetes container (EKS)