pipeline {

    agent any

    tools {
        maven 'Maven 3.8.7'
        jdk 'Java 11'
    }

    environment {
        TOMCAT_URL = 'http://<Tomcat-IP>:8080'
        APP_PATH = '/mywebapp'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-username>/<your-repo>.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tomcat-creds',
                                usernameVariable: 'USER',
                                passwordVariable: 'PASS')]) {

                    sh '''
                        WAR=$(ls target/*.war)
                        echo "Deploying application..."
                        curl --fail -u $USER:$PASS -T $WAR \
                        "$TOMCAT_URL/manager/text/deploy?path=$APP_PATH&update=true"
                    '''
                }
            }
        }
    }

    post {
        success { echo "Deployment successful!" }
        failure { echo "Deployment failed!" }
    }
}
