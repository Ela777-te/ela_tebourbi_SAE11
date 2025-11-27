pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "elatebourbi/student-management"
        VERSION = "${env.BUILD_ID}"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Récupération du code source..."
                git branch: 'main', url: 'https://github.com/Ela777-te/ela_tebourbi_SAE11.git'
            }
        }

        stage('Build JAR') {
            steps {
                echo "🔨 Construction du JAR avec Maven..."
                sh 'mvn clean package -DskipTests'
            }
            post {
                success { echo "✅ Build Maven réussi!" }
                failure { error "❌ Échec du build Maven" }
            }
        }

        stage('Test Docker') {
            steps {
                echo "🐳 Vérification de Docker..."
                sh '''
                    docker --version
                    docker ps || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Construction de l'image Docker..."
                sh """
                    docker build -t ${IMAGE_NAME}:${VERSION} .
                    docker tag ${IMAGE_NAME}:${VERSION} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "📤 Authentification sur Docker Hub..."
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "\$DOCKER_PASS" | docker login -u "\$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${VERSION}
                        docker push ${IMAGE_NAME}:latest
                        docker logout
                    """
                }
            }
            post {
                success { echo "✅ Images poussées avec succès vers Docker Hub!" }
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline exécuté avec succès!"
            echo "✅ Image Docker: ${IMAGE_NAME}:${VERSION}"
            echo "✅ Disponible sur Docker Hub !"
        }
        failure { echo "💥 Échec du pipeline!" }
    }
}
