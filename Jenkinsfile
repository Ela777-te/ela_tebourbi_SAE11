pipeline {
 agent any
 tools {jdk 'JAVA_HOME’, maven 'M2_HOME'}
 stages {
 stage('GIT') {
           steps {
               git branch: 'main',
               url: ' https://github.com/Ela777-te/ela_tebourbi_SAE11.git'
          }
     }
 stage ('Compile Stage') {
 steps {
 sh 'mvn clean compile'
 }
 }
 }
}
