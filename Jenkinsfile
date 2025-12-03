pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "elatebourbi/student-management"
        VERSION = "${env.BUILD_ID}"
    }

    stages {
        stage('GIT Checkout') {
            steps {
                echo "📦 Récupération du code source..."
                git branch: 'main',
                    url: 'https://github.com/Ela777-te/ela_tebourbi_SAE11.git'
            }
        }

        stage('Build') {
            steps {
                echo "🔨 Construction du JAR avec Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                echo "🐳 Construction de l'image Docker..."
                script {
                    sh """
                        DOCKER_HOST=unix:///var/run/docker.sock docker build -t ${IMAGE_NAME}:${VERSION} .
                        DOCKER_HOST=unix:///var/run/docker.sock docker tag ${IMAGE_NAME}:${VERSION} ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                echo "📤 Pousser les images vers Docker Hub..."
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKERHUB_CREDENTIALS_USR',
                        passwordVariable: 'DOCKERHUB_CREDENTIALS_PSW'
                    )]) {
                        sh """
                            DOCKER_HOST=unix:///var/run/docker.sock \
                            echo "\$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "\$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                            DOCKER_HOST=unix:///var/run/docker.sock docker push ${IMAGE_NAME}:${VERSION}
                            DOCKER_HOST=unix:///var/run/docker.sock docker push ${IMAGE_NAME}:latest
                            DOCKER_HOST=unix:///var/run/docker.sock docker logout
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "🎉 SUCCÈS TOTAL !"
            echo "✅ Image Docker construite: ${IMAGE_NAME}:${VERSION}"
            echo "✅ Image poussée vers Docker Hub !"
        }
        failure {
            echo "❌ Échec du pipeline."
        }
    }
}
