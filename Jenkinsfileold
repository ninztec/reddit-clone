pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'NodeJS'
    }
    environment {
        SCANNER_HOME=tool 'SonarQubeScanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                timeout(time: 5, unit: 'MINUTES') { // Set your desired timeout duration
                    git branch: 'main', url: 'https://github.com/ninztec/reddit-clone.git'
                }
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('SonarQube') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Reddit \
                    -Dsonar.projectKey=Reddit '''
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    }
}
