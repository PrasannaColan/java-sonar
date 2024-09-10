pipeline {
    agent any

    environment {
        qualityGateUrl = 'http://localhost:9000/api/qualitygates/project_status?projectKey=test-pipeline&token=sqa_ffd7389d92ead64b393a693d2fb47d82f42c64f3'
        SONARQUBE_API_TOKEN = 'sqa_ffd7389d92ead64b393a693d2fb47d82f42c64f3' 
        SONARQUBE_SERVER_URL = 'http://localhost:9000' 
        SONARQUBE_PROJECT_KEY = 'test-pipeline' 
       //  NODE_VERSION = '20' 
    }

    stages {
        
        stage('Build & Analysis') {
            steps {
              //  withSonarQubeEnv('http://localhost:9000') {
                sh 'mvn clean package sonar:sonar'
             // }
            }
        }
        stage('Test') {
            steps { 
                sh "mvn test"
            }
        }
      /*   stage('Install Dependencies') {
            steps {
                script {
                    // Use NodeJS Plugin to set up Node.js
                    tool name: 'NodeJS', type: 'NodeJS'
                    sh 'npm install' // Install project dependencies
                }
            }
        }

        stage('Run Tests with Coverage') {
            steps {
                script {
                    // Run Jest tests and collect coverage
                    sh 'npm test -- --coverage' // Run tests with coverage collection
                }
            }
        } */

         stage('Cleanup Workspace') {
    steps {
        deleteDir() 
    }
}
        
        stage('Check Quality Gate') {
            steps {
               script {
                    def response = sh(script: "curl -u ${SONARQUBE_API_TOKEN}: ${qualityGateUrl}", returnStdout: true).trim()
                    
                    def qualityGateStatus = readJSON text: response
                    def status = qualityGateStatus.projectStatus.status
                    
                    if (status != 'OK') {
            
                        error "SonarQube quality gate status: ${status}"
                        currentBuild.result = 'FAILURE'
                        
                    } 
                   else { 
                       echo "SonarQube quality gate status: ${status}"
                      currentBuild.result = 'SUCCESS'
               }
                }
            }
        }
          stage('Pipeline Status') {
                steps {
                
            echo "Pipeline finished with status: ${currentBuild.result}"
    
}
    }
                }

   
}
