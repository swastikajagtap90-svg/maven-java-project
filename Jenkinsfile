
pipeline {
    agent any

    tools {
        jdk 'JDK-17'
        maven 'Maven-3.9'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Fetching source code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Verifying Java and Maven versions...'
                sh 'java -version'
                sh 'mvn -version'

                echo 'Compiling the Java application...'
                sh 'mvn clean compile'
            }
        }

        stage('Code Quality - Checkstyle') {
            steps {
                echo 'Checking code quality...'
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Unit Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging the application into a JAR...'
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar',
                                 fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application artifact...'
                sh '''
                    set -e
                    mkdir -p /tmp
                    cp target/*.jar /tmp/deployed-app.jar
                    echo "Deployment completed successfully."
                    ls -lh /tmp/deployed-app.jar
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
