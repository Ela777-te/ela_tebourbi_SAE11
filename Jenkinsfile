pipeline {
    agent any

    tools {
        jdk 'jdk17'       // Nom exact du JDK configuré dans Jenkins
        maven 'Maven'     // Nom exact du Maven configuré dans Jenkins
    }

    stages {
        stage('GIT') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Ela777-te/ela_tebourbi_SAE11.git'
            }
        }

        stage('Compile Stage') {
            steps {
                sh 'mvn clean compile'
            }
        }
    }
}
