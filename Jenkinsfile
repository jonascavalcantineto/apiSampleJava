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
    def registry = ""

    
    try {
           
        stage(name: "checkout") {
            checkout scm
        }

        stage(name: "build") {
            sh "${mvn} clean install -DskipTests"
        }

        stage(name: "test") {
            sh "${mvn} test"
            
            step([$class: "JUnitResultArchiver", testResults: "**/build/test-results/test/TEST-*.xml"])
        }

        stage(name: "release") {
            // tagVersion = getVersionGenerateRelease(branch)
            // generateDockerBuild(projectGroup, projectName, registry, tagVersion)
        }

        stage(name: "deploy") {

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
    docker.withRegistry("${registry}", "svcacc_ps_jenkins") {
        app.push(tagVersion)
    }
}