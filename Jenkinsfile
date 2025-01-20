pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-docker-image-name"
        DOCKER_REGISTRY = "your-docker-registry"
        OWASP_ZAP_DOCKER_IMAGE = "owasp/zap2docker-stable"
    }

    stages {
        stage('Unit Test') {
            steps {
                script {
                    // Menjalankan unit test
                    sh 'pytest'
                }
            }
        }

        stage('Build & Push Docker') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE .'
                    // Push Docker image ke registry
                    sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE'
                }
            }
        }

        stage('OWASP') {
            steps {
                script {
                    // Menjalankan OWASP ZAP untuk scanning
                    sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://your-application-url'
                }
            }
        }

        stage('Security Container or Library Vulnerabilities') {
            steps {
                script {
                    // Menjalankan scanning untuk vulnerabilities
                    sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image $DOCKER_REGISTRY/$DOCKER_IMAGE'
                }
            }
        }

        stage('Deployment') {
            steps {
                script {
                    // Menjalankan deployment
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            // Membersihkan workspace setelah build selesai
            cleanWs()
        }
    }
}