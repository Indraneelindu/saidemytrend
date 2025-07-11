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
    }
}
