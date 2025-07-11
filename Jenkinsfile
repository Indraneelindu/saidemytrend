// Define the URL of the Artifactory registry
def registry = 'https://trialp4bu3w.jfrog.io/'



pipeline {
    agent any 
    environment {
        PATH = "/opt/maven/maven/bin:$PATH"
    }
    stages {
        stage ("Build Started") {
            steps {
                sh "mvn clean deploy -Dmaven.test.skip=true"
                echo ("Build is Completed")
            }
        }
        stage ("Testing is Run") {
            steps {
                echo "Testing is started from here"
                sh "mvn surefire-report:report"
                echo "Testing is Completed"
            }
        }
        stage ("SonarQube Analysis") {
            environment {
                scannerHome = tool "sonar-scanner"
            }
            steps {
                withSonarQubeEnv("sonarqube-server"){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "Artifact-cred"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "indu-libs-release-local/{1}",
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo '<--------------- Jar Publish Ended --------------->'
                }
            }
        }
    }
}
