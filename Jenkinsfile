#!groovy

node { 
    
    def mvnHome = tool name: 'maven', type: 'maven'
    def mvn = "${mvnHome}/bin/mvn"
    DOCKER_HOME = tool "docker"
    def docker = "$DOCKER_HOME"

    def branch = getGitBranchName()
    if(branch == "*/master")
        branch = "prod" 
    else if (branch == "*/qa")
        branch = "qa" 

    echo "branch: ${branch}"

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
            // dir('.') {
            //     docker.build("jonascavalcantineto/${projectName}:${branch}").push()
            // }
            sh "echo $PATH"
            sh "export PATH=$PATH:$DOCKER_HOME"
            
            sh "${docker} ps"
            docker.withRegistry(registry, 'dockerhub') {
                docker.build("jonascavalcantineto/${projectName}:${branch}").push()
                
            }   
            //generateDockerBuild(projectName, registry, branch,docker,'docker-credentials')     
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

def generateDockerBuild(projectName, registry,branch, docker, credentialsId) {
    sh "docker login -u user -p pass"
    sh "echo docker build ${registry}/${projectName}:${branch}"

    docker.withRegistry(registry, credentialsId) {

        def customImage = docker.build("${projectName}:${branch}")

        /* Push the container to the custom Registry */
        customImage.push()
    }

}

def deploy(branch){
    sh "echo kubectl -n ${branch} apply -f deploy.yaml"
}