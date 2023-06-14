pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    parameters {
        choice(name: 'DEPLOY_TO', choices: ['dev', 'qa', 'prod'], description: 'Where to deploy?')
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
                    sh "aws s3 cp target/vprofile-${version}.war s3://automatio999/vprofile-artifacts/vprofile-${version}.war"
                }
            }
        }
        stage('Deploy-qa') {
            when {
                expression { params.DEPLOY_TO == 'dev' }
            }
            steps {
                sshagent(credentials: ['ubuntu']) {
                    sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-v1.war /var/lib/tomcat9/webapps/'"
                    sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
                }
            }
        }
        stage('Deploy-stage') {
            when {
                expression { params.DEPLOY_TO == 'qa' }
            }
            steps {
                sshagent(credentials: ['ubuntu']) {
                    sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-v1.war /var/lib/tomcat9/webapps/'"
                    sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
                }
            }
        }
        stage('Deploy-prod') {
            when {
                expression { params.DEPLOY_TO == 'prod' }
            }
            steps {
                sshagent(credentials: ['ubuntu']) {
                    sh "ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-v1.war /var/lib/tomcat9/webapps/'"
                    sh "ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'"
                }
            }
        }
    }
}
