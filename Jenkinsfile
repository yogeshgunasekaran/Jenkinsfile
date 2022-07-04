def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any

    stages{
        stage('Fetch code') {
          steps{
              git branch: '<branch-name-here>', url:'<git-repository-code-link-here>'
          }  
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps {
                sh 'mvn test'
            }

        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool '<global-tool-configuration-sonarqube-scanner-name-here>'
            }
            steps {
               withSonarQubeEnv('<configure-system-sonarqube-server-name-here>') {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=<project-key-here> \
                   -Dsonar.projectName=<project-name-here> \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/<path-here>/<path-here>/<path-here>/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Upload Artifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: '<sonar-server-private-ip-here>:8081',
                  groupId: '<group-id-here>',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: '<nexus-hosted-repo-name-here>',
                  credentialsId: '<jenkins-global-credentials-nexuslogin-ID-here>',
                  artifacts: [
                    [artifactId: '<project-name-here>',
                     classifier: '',
                     file: 'target/<artifact-name-here>.war',
                     type: 'war']
                  ]
                )
            }
        }
    }
    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#<slack-channel-name-here>',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
    
}
