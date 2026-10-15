pipeline {
    agent any
    
    environment {
        DOCKER_CRED = 'docker-hub-credentials'
        IMAGE_REPO = 'your-dockerhub-username/my-java-app'
    }
    
    stages {
        stage('1. Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kp-reddy7676/jenkins-project-1.git'
            }
        }
        
        stage('2. Environment Setup') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
            }
        }
        
        stage('3. Build & Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('4. Unit Testing') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('5. Static Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('6. Security Vulnerability Scan') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
            post {
                always {
                    dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
                }
            }
        }
        
        stage('7. Package Artifact') {
            steps {
                sh 'mvn package -DskipTests'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        
        stage('8. Docker Containerization') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CRED}") {
                        def appImage = docker.build("${IMAGE_REPO}:${env.BUILD_NUMBER}")
                        appImage.push()
                        appImage.push('latest')
                    }
                }
            }
        }
        
        stage('9. Automated Deployment') {
            steps {
                echo "Deploying version ${env.BUILD_NUMBER} to target environment..."
            }
        }
    }
    
    post {
        success {
            echo "Pipeline executed successfully across all 9 stages!"
        }
        failure {
            echo "Pipeline failed! Please check logs for details."
        }
    }
}
EOF
