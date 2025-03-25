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

        
      
        }
    
