pipeline {
    agent any

    tools {
        maven 'Maven'      // Jenkins → Global Tool Configuration → Maven
    }

    environment {
        TOMCAT_IP = "172.31.67.95"   // Put your Tomcat private IP
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sharath624/my-maven-app.git'
            }
        }

        stage('Build Artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tomcat-creds',
                                                 usernameVariable: 'TUSER',
                                                 passwordVariable: 'TPASS')]) {

                    sh """
                    curl -u $TUSER:$TPASS \
                    --upload-file target/*.war \
                    "http://${TOMCAT_IP}:8080/manager/text/deploy?path=/myapp&update=true"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
