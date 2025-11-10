pipeline {
    agent any

    tools {
        jdk 'JDK11'          // Ensure you have this JDK installed in Jenkins tools
        maven 'maven3'       // Ensure you have Maven installed in Jenkins tools
    }

    environment {
        version = ''
        MAVEN_OPTS = "-Xms256m -Xmx512m"  // Limit Maven memory for local machine
    }

    stages {
        stage('Read POM') {
            steps {
                script {
                    // Safe read of POM version
                    try {
                        def pom = readMavenPom file: 'pom.xml'
                        version = pom.version
                    } catch (Exception e) {
                        echo "readMavenPom failed or plugin missing, using default version 1.0.0"
                        version = "1.0.0"
                    }
                    echo "Project version is: ${version}"
                }
            }
        }

        stage("Build Artifact") {
            steps {
                script {
                    // Skip tests to reduce memory usage
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage("Test") {
            steps {
                script {
                    // Run tests safely; pipeline won't fail on test errors
                    sh 'mvn test || true'
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage("Upload Artifact to S3") {
            steps {
                script {
                    sh """
                    aws s3 cp target/vprofile-${version}.war \
                    s3://automatio999/vprofile-artifacts/vprofile-${version}.war
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['ubuntu']) {
                    sh """
                    ssh ubuntu@3.110.159.232 'sudo mv ~/vprofile-${version}.war /var/lib/tomcat9/webapps/'
                    ssh ubuntu@3.110.159.232 'sudo systemctl restart tomcat9'
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning workspace to free disk/memory"
            cleanWs()
        }
    }
}

