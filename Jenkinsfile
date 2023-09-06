pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    environment {
        version = ''
    }
    stages {
        stage('Read POM') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    version = pom.version
                    echo "Project version is: ${version}"
                }
            }
        }
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
        stage("Upload Artifact s3") {
            steps {
                script {
                    sh "aws s3 cp vprofile-1.0.3.war s3://vamsi2000/vprofile-artifact/vprofile-1.0.3.war"
                }
            }
        }
        stage('Deploy') {
        steps {
            sshagent(credentials: ['ubuntu']) {
                sh "scp target/vprofile-1.0.3.war ubuntu@3.110.159.232:~/"
                sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-1.0.3.war /var/lib/tomcat9/webapps/'"
                sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
            }
        }
    }
    }
}
