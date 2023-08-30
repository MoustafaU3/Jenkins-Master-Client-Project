def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]

pipeline {
    agent {
        label 'Maven-Build-Env'
    }
    stages {
        stage('Validate Project') {
            steps {
                sh 'mvn validate'
            }
        }
        stage('Unit Test'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Test'){
            steps {
                sh 'mvn verify -DskipTests'
            }
        }
        stage('App Packaging'){
            steps {
                sh 'mvn package'
            }
        }
        stage('Checkstyle Code Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Inspection') {
            steps {
                sh  """mvn sonar:sonar \
                       -Dsonar.projectKey=maven-java-webapp \
                       -Dsonar.host.url=http://172.31.52.45:9000 \
                       -Dsonar.login=bc8bfd46f8b08bc7e0e4f407d44cbeb66d5c35cf"""
            }
        }
        stage("Upload Artifact To Nexus"){
            steps{
                sh 'mvn deploy'
            }
            post {
                success {
                    echo 'Successfully Uploaded Artifact to Nexus Artifactory'
                }
            }
        }
    }
    post {
        always {
            echo 'Slack Notifications.'
            slackSend(
                channel: '#moustafa-jenkins-master-client-alerts',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER} \n Build Timestamp: ${env.BUILD_TIMESTAMP} \n Project Workspace: ${env.WORKSPACE} \n More info at: ${env.BUILD_URL}"
            )
        }
    }
}
