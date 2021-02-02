#!groovy

node { 
    
    def mvnHome = tool name: 'maven', type: 'maven'
    def mvn = "${mvnHome}/bin/mvn"

    def branch = getGitBranchName()
    if(branch == "*/master")
        branch = "prod" 
    else if (branch == "*/qa")
        branch = "qa" 

    echo "branch: ${branch}"

    def projectGroup = "jonascavalcantineto"
    def projectName = "apisample"
    def registry = "https://docker.io"

    
    try {
        
        stage(name: "checkout") {
            checkout scm
        }

        stage(name: "build") {
            sh "${mvn} clean install -DskipTests"
        }

        stage(name: "test") {
            sh "${mvn} test"
            
            step([$class: "JUnitResultArchiver", allowEmptyResults: true, testResults: "**/build/test-results/test/TEST-*.xml"])
        }

        stage(name: "release-image") {
            generateDockerBuild(projectGroup, projectName, registry, branch)  
        }

        stage(name: "deploy") {
            deploy(branch,projectName)
        }

    } catch (Exception e) {
        throw e
    }
}

def getGitBranchName() {
    return scm.branches[0].name
}

def generateDockerBuild(projectGroup, projectName, registry, tagVersion) {

    sh "echo Building"
    def app = docker.build("${projectGroup}/${projectName}")
    sh "echo Pushing"
    app.push(tagVersion)
}

def deploy(branch,projectName){
    sh "echo Doing Deploy on Kubernetes in ${branch} environment"
    sh "kubectl get pods --all-namespaces"
    sh "sed -i 's/CI_PROJECT_NAME/${projectName}/g' deployment.yaml"
    sh "sed -i 's/CI_COMMIT_REF_NAME/${branch}/g' deploy.yaml"
    sh "kubectl -n ${branch} apply -f deploy.yaml"
    sh "kubectl rollout status deployment ${projectName}"
}