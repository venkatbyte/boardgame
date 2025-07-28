pipeline {
    agent any
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/venkatbyte/boardgame.git'
            }
        }
        
        stage('Test') {
            steps {
               sh 'mvn test'
            }
        }
        
        
        stage('Quality Check') {
            steps {
                timeout(time: 2, unit: 'SECONDS') {
                    withSonarQubeEnv('sonar-server') {
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=boardgame -Dsonar.projectKey=boardgame \
                        -Dsonar.java.binaries=.
                        '''
                    }
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Publish to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: '', jdk: '', maven: '', mavenSettingsConfig: 'local-maven-settings', traceability: true) {
                    sh 'mvn deploy'
                }               
            }
        }
    }
}
