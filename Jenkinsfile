pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK 11'
    }
    stages {
        // Run unit test in all cases
        stage('Unit Test') {
            steps {
                    echo BRANCH_NAME
                    sh 'mvn -Dmaven.test.failure.ignore=true clean test site'
                    jacoco()
                    recordIssues(tools: [checkStyle(), findBugs(useRankAsPriority: true), pmdParser()])
            }
            post {
                always {
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        stage('SonarQube analysis') {
             environment {
                scannerHome = tool 'SonarQube Scanner'
            }
            steps {
                withSonarQubeEnv(installationName: 'SonarQube', credentialsId: 'SonarQube Server') { // If you have configured more than one global server connection, you can specify its name
                     println "${env.SONAR_HOST_URL}" 
                     println "${env.SONAR_AUTH_TOKEN}" 
                    sh "${scannerHome}/bin/sonar-scanner -X"
                }
            }
        }
    }
}
