pipeline {
    agent any

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-arm64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "elatebourbi/student-management"
        VERSION = "${env.BUILD_ID}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Récupération du code source..."
                git branch: 'main', url: 'https://github.com/Ela777-te/ela_tebourbi_SAE11.git'
            }
        }

        stage('Build Maven') {
            steps {
                echo "🔨 Compilation du projet avec Maven (Java 17)..."
                sh './mvnw clean package -DskipTests'
            }
            post {
                success {
                    echo "✅ Build Maven réussi!"
                }
                failure {
                    echo "❌ Échec du build Maven"
                }
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

        stage('Push Docker Hub') {
            steps {
                echo "📤 Pousser l'image sur Docker Hub..."
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                            echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                            docker push ${IMAGE_NAME}:${VERSION}
                            docker push ${IMAGE_NAME}:latest
                            docker logout
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline terminé avec succès !"
            echo "✅ Image Docker construite et poussée : ${IMAGE_NAME}:${VERSION}"
        }
        failure {
            echo "💥 Échec du pipeline !"
        }
    }
}
