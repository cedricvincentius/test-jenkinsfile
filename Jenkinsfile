pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "docker.private.registry.com:5000/test"
        DOCKER_CREDENTIALS_ID = "docker-credentials"
        KUBECONFIG_CREDENTIALS_ID = "kubeconfig-credentials"
    }

    stages {
        stage('Unit Test') {
            steps {
                script {
                    // Run unit tests
                    sh 'echo "Running unit tests..."'
                    // Add your unit test command here, e.g., `mvn test` or `npm test`
                }
            }
        }

        stage('Build & Push Docker') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
                    // Push Docker image
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin docker.private.registry.com:5000'
                        sh 'docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                    }
                }
            }
        }

        stage('OWASP') {
            steps {
                script {
                    // Run OWASP ZAP scan
                    sh 'echo "Running OWASP ZAP scan..."'
                    // Add your OWASP ZAP scan command here
                }
            }
        }

        stage('Security Container or Library Vulnerabilities') {
            steps {
                script {
                    // Run security scan for container or library vulnerabilities
                    sh 'echo "Running security scan for container or library vulnerabilities..."'
                    // Add your security scan command here, e.g., `trivy image ${DOCKER_IMAGE}:${BUILD_NUMBER}`
                }
            }
        }

        stage('Deployment') {
            steps {
                script {
                    // Deploy to Kubernetes
                    withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                        sh 'kubectl apply -f k8s/deployment.yaml'
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
