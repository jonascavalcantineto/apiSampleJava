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

    def projectName = "apisample"
    def registry = "docker.io/jonascavalcantineto"

    
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

        stage(name: "release") {
            generateDockerBuild(projectName, registry, branch)
        }

        stage(name: "deploy") {
            deploy(branch)
        }

    } catch (Exception e) {
        throw e
    }
}

def getGitBranchName() {
    return scm.branches[0].name
}

def generateDockerBuild(projectName, registry,branch) {
    sh "echo docker build ${registry}/${projectName}:${branch}"
}

def deploy(branch){
    sh "echo kubectl -n ${branch} apply -f deploy.yaml"
}