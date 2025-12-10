pipeline {
    agent any

    tools {
        maven 'Maven 3.8.7' // Jenkins → Global Tool Configuration → Maven
    }

    environment {
        TOMCAT_IP = "172.31.67.95" // Tomcat private IP
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

                    // Make sure the .war file exists before deploying
                    sh '''
                    WAR_FILE=$(ls target/*.war | head -n 1)
                    if [ -z "$WAR_FILE" ]; then
                        echo "WAR file not found!"
                        exit 1
                    fi

                    echo "Deploying $WAR_FILE to Tomcat..."
                    curl -u $TUSER:$TPASS \
                        --upload-file "$WAR_FILE" \
                        "http://${TOMCAT_IP}:8080/manager/text/deploy?path=/myapp&update=true"
                    '''
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
