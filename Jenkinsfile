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
            // docker.withRegistry(registry, 'dockerhub') {
            //     docker.build("jonascavalcantineto/${projectName}:${branch}").push()
            // } 

            generateDockerBuild(projectGroup, projectName, registry, branch)  
              
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

def generateDockerBuild(projectGroup, projectName, registry, tagVersion) {
    
    app = docker.build("${projectGroup}/${projectName}", ".")
    docker.withRegistry("${registry}", "dockerhub") {
        app.push(tagVersion)
    }
}

def deploy(branch){
    sh "echo kubectl -n ${branch} apply -f deploy.yaml"
    sh "kubectl get pods --all-namespaces"
}