# Gilab Pipeline Solution

Following the **Devops CAML** principle **(Culture, Automation, LeanIT, Measurement and Sharing)**

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

Solutions implemented or (not implementation in this lab but I use)

**SCM**: GitLab (implemented)

**Build**: Docker (implemented)

**Tests**: Sonarqube (Code tests) (not implementation in this lab but I use) | Nexus (Libs Repository and Docker Registry) (not implementation in this lab but I use)

**Pipeline**: GitLab CI (implemented)

**Container Orchestration**: Kubernetes (implemented)

**Cloud**: AWS (implemented)

**Observability**: Prometheus (CPU metrics x Memory X Disk collector)(not implementation in this lab but I use) | Grafana (CPU metrics UI x Memory X Disk)(not implementation in this lab but I use)| ELK (Logs Management of all containers in the environment)(not implementation in this lab but I use)

**Providently from Infrastructure**: Terraform (AWS Resource Generation) ((not implementation in this lab but I use))

We will explain how CICD is carried out through Gihub. Gitlab will use the pipeline of the structure itself. Follows drawing bellow the flow:
### Architecture

![GitLab Pipeline](https://GitLab.com/jonascavalcantineto/apiSampleJava/blob/master/docs/gitlab/GitLab-CICD-Pipeline.png)

**SCM (Source Code Manager)**

The developer will follow a stream of branchs with the following names: hotfix, feature, qa, production and master

**Hotfix:**: Branch to resolve a system bug

**Feature:**: Branch for new feature implementations

**QA:**: It will be the branch for testing and generating the system version. She does not need confirmation from someone responsible
Production: Branch that will be responsible for generating the deployment in the production environment. In this phase, a QA version tag is made and deployed to a container orchestrator.

**Master:**: Branch for generating the realease version of the system in TAG 

**Format of Hotfix and Feature branches**

hotfix/email/task-number

feature/email/task-number

**GitLab - Pipeline through the Jenkinsfile**

The following stages will be held

    - Build: System package construction

    - Test: Conducting code tests

    ** RollBack: Rollback in the GitLab Commits and deploy to Kubernetes

    - Release Image: Building container image

    - Deploy: Deploy to Kubernetes (EKS)