pipeline {
    agent any
    
    environment {
                SONAR_URL = 'https://sonarcloud.io/'
                SONAR_ORG = 'devsecops1-kb'
                SONAR_PROJECTKEY = 'devsecops1-kb'
                SONAR_LOGIN = '6388cfceaadf0f5725a5921b0d8dffb0f4648d53'
            }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/kbrepository/test-java-node0.git']]
                ])
            }
        }
        stage('Build') {
            steps {
                sh 'mvn install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube analysis') {
            
            steps {
                    sh "mvn clean verify sonar:sonar -Dsonar.host.url=${env.SONAR_URL} -Dsonar.organization=${env.SONAR_ORG} -Dsonar.projectKey=${env.SONAR_PROJECTKEY} -Dsonar.login=${env.SONAR_LOGIN}"
                }
        }
        stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml' // Publish test results
            cleanWs() // Clean workspace after build
        }
    }
}
}
