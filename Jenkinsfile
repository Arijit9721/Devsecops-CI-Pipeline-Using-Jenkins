pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'Maven-tool'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Arijit9721/Devsecops-CI-Pipeline-Using-Jenkins.git'
            }
        }
        
         stage('Maven Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        
         stage('Gitleaks') {
            steps {
                sh 'gitleaks detect --source . -r gitleaks-report.txt'
            }
        }
        
         stage('Trivy Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-report.html . '
            }
        }
        
         stage('Maven Test') {
            steps {
                sh 'mvn test'
            }
        }
        
         stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' 
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BG \
                    -Dsonar.projectKey=BG \
                    -Dsonar.java.binaries=target
                    '''
                }
            }
        }
        
         stage('Quality Check') {
            steps {
               timeout(time: 5, unit:'MINUTES') {
                   waitForQualityGate abortPipeline: false
               }
            }
        }
        
         stage('Maven Build') {
            steps {
                sh 'mvn package'
            }
        }
        
    }
}
