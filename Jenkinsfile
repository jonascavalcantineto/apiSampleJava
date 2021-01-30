#!groovy

node("java11") {

    def javaHome = tool 'java11'
    env.PATH = "${env.PATH}:~/.local/bin:${javaHome}/bin"
    env.JAVA_HOME = "${javaHome}"

    def branch = getGitBranchName()
    if(branch == "*/master")
        branch = "prod" 
    else if (branch == "*/qa")
        branch = "qa" 

    echo "projectEnv: ${projectEnv}"
    echo "branch: ${branch}"

    if (projectEnv == " " || projectEnv == null){
        if (branch == "master" || branch == "prod")
            projectEnv = "prod"
        else 
            projectEnv = "qa"
    }

    echo "projectEnv setted: ${projectEnv}"

    def projectName = "apisample"
    def registry = "https://registry.intranet.pagseguro.uol"

    
    try {
    
        deleteDir()
       
        def mvn = "./mvnw"
        
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
            tagVersion = getVersionGenerateRelease(branch)
            generateDockerBuild(projectGroup, projectName, registry, tagVersion)
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