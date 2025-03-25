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
                sh 'mvn clean install || true'  // Always proceed, analyze errors next
            }
        }
        stage('Evaluate Test Results') {
            steps {
                script {
                    // Parse number of errors from Surefire report summary
                    def summaryFile = 'target/surefire-reports/TEST-summary.txt'
                    sh """
                        errors=\$(grep -oP '(?<=Failures: )\\d+' ${summaryFile} | awk '{sum += \$1} END {print sum}')
                        if [ -z "\$errors" ]; then errors=0; fi
                        echo "Total Test Failures: \$errors"
                        if [ "\$errors" -ge 6 ]; then
                            echo "Build failed: Too many test errors (\$errors)."
                            exit 1
                        else
                            echo "Build continues: Test errors within limit (\$errors)."
                        fi
                    """
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-9') {
                    sh '''
                        mvn sonar:sonar \
                          -DargLine="--add-opens java.base/java.lang=ALL-UNNAMED \
                          --add-opens java.base/java.util=ALL-UNNAMED" \
                          -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }
}

        
        stage('build'){
            steps{
                script{
                    sh 'docker build -t rehan1114/myimg .'
                }
            }
        }
        
         stage('login'){
            steps{
                script{
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                }
            }
        }
        
        stage('push'){
            steps{
                script{
                    sh 'docker push rehan1114/myimg'
                }
            }
        }
        
         stage('docker run'){
            steps{
                script{
                    sh 'docker run -d --name my-cont -p 8081:8081 rehan1114/myimg'
                }
            }
        }
    }
