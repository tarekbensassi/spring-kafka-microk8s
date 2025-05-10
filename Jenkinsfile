pipeline {
    agent any

    environment {
     GITHUB_CREDENTIALS_ID = 'github-ssh-key'   // ID des credentials SSH dans Jenkins
        GIT_BRANCH = 'main'                        // Branche par défaut (modifiable si nécessaire)
        IMAGE_NAME = "spring-kafka-app"
        IMAGE_TAG = "localbuild-${BUILD_NUMBER}"
    }

    stages {

    
        
          stage('Checkout from GitHub') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: env.GITHUB_CREDENTIALS_ID, keyFileVariable: 'ssh-key')]) {
                    git branch: env.GIT_BRANCH,
                        credentialsId: env.GITHUB_CREDENTIALS_ID,
                        url: 'git@github.com:tarekbensassi/spring-kafka-microk8s.git' 
                }
            }
        }


        stage('Start Kafka with Docker Compose') {
            steps {
                sh 'docker-compose up -d'
                sh 'sleep 10' // Laisser Kafka démarrer
            }
        }

     /*   stage('Build JAR') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }*/

      stage('Build Docker Image for MicroK8s') {
    steps {
        sh '''
             microk8s status --wait-ready
             microk8s docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
        '''
    }
}


    stage('Deploy to MicroK8s') {
    steps {
        sh '''
            # S'assurer que /snap/bin est dans le PATH
            export PATH=$PATH:/snap/bin

            # S'assurer que MicroK8s est prêt
             microk8s status --wait-ready

            # Supprimer l'ancien déploiement si présent
             microk8s kubectl delete deployment spring-kafka-app --ignore-not-found=true

            # Appliquer les fichiers de configuration
             microk8s kubectl apply -f k8s/deployment.yaml
             microk8s kubectl apply -f k8s/service.yaml

            # Mettre à jour l'image du déploiement
             microk8s kubectl set image deployment/spring-kafka-app spring-kafka-app=${IMAGE_NAME}:${IMAGE_TAG}
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
