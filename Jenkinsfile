pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')  // Every 2 mins
        githubPush()           // Webhook trigger
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sharath624/my-maven-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = "target/myapp.war"
                    def tomcatURL = "http://172.31.67.95:8080/manager/text"

                    sh """
                    curl --upload-file ${warFile} "${tomcatURL}/deploy?path=/myapp&update=true" \
                    --user admin:deployuser
                    """
                }
            }
        }
    }
}
