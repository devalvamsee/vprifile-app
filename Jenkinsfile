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
                sh "sh "scp target/vprofile-v1.war ubuntu@3.110.159.232:~/"
                sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-v1.war /var/lib/tomcat9/webapps/'"
                sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
            }
        }
    }

    }
}
