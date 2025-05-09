pipeline {
    agent any

    environment {
        IMAGE_NAME = "spring-kafka-app"
        IMAGE_TAG = "localbuild-${BUILD_NUMBER}"
    }

    stages {
     stage('Checkout') {
    steps {
        sshagent(['git-ssh-key']) {
            git url: 'git@github.com:tarekbensassi/spring-kafka-microk8s.git', credentialsId: 'git-ssh-key'
        }
    }
}
        stage('Start Kafka with Docker Compose') {
            steps {
                sh 'docker-compose up -d'
                sh 'sleep 10' // Laisser Kafka démarrer
            }
        }

        stage('Build JAR') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image for MicroK8s') {
            steps {
                sh "microk8s.docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy to MicroK8s') {
            steps {
                sh '''
                    microk8s.kubectl delete deployment spring-kafka-app --ignore-not-found=true
                    microk8s.kubectl apply -f k8s/deployment.yaml
                    microk8s.kubectl apply -f k8s/service.yaml
                    microk8s.kubectl set image deployment/spring-kafka-app spring-kafka-app=${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline terminé"
        }
        failure {
            echo "❌ Échec du déploiement"
        }
    }
}
