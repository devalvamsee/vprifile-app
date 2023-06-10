pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    stages {
        stage("Build Artifact") {
        steps {
            script {
                 sh 'mvn clean package -DskipTests'
            }
        }
        }
        stage("Test") {
        steps {
            script {
                 sh 'mvn test'
            }
        }
        }
        stage("Upload Artifacr s3") {
        steps {
            script {
                 sh 'aws s3 vprofile-v1.war s3://automatio999/vprofile-artifacts/vprofile-v1.war'
            }
        }
        }
    stage('Deploy') {
        steps {
            sshagent(credentials: ['ubuntu']) {
                sh "scp target/vprofile-v1.war ubuntu@3.110.159.232:~/"
                sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-v1.war /var/lib/tomcat9/webapps/'"
                sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
            }
        }
    }

    }
}