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
    stage('Deploy') {
        steps {
            sshagent(credentials: ['ubuntu']) {
                sh "sc target/vprofile.war ubuntu@3.110.159.232:~/"
                sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile.war /var/lib/tomcat9/webapps/'"
                sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
            }
        }
    }

    }
}