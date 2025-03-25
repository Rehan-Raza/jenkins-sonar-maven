pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Rehan-Raza/jenkins-sonar-maven.git' , branch: 'master'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean'  // Always proceed, analyze errors next
            }
        }
    }
}
    
