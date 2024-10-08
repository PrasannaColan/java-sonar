pipeline {
    agent any

    environment {
        qualityGateUrl = 'http://localhost:9000/api/qualitygates/project_status?projectKey=sonar&token=sqp_771bdc133aa2157b35b47b8fdb399803e047664a'
        SONARQUBE_API_TOKEN = 'sqp_771bdc133aa2157b35b47b8fdb399803e047664a' 
        SONARQUBE_SERVER_URL = 'http://localhost:9000' 
        SONARQUBE_PROJECT_KEY = 'sonar' 
       //  NODE_VERSION = '20' 
    }

    stages {
        
        stage('Build & Analysis') {
            steps {
              //  withSonarQubeEnv('http://localhost:9000') {
                sh 'mvn clean package'
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
                    // Using curl to get the quality gate status from SonarQube
                    def response = sh(script: "curl -u ${SONARQUBE_API_TOKEN}: ${qualityGateUrl}", returnStdout: true).trim()

                    // Ensure response is not empty before processing
                    if (response) {
                        // Read the response as JSON
                        def qualityGateStatus = readJSON text: response

                        // Extract the status from the response
                        def status = qualityGateStatus.projectStatus.status

                        if (status != 'OK') {
                            error "SonarQube quality gate status: ${status}"
                        } else {
                            echo "SonarQube quality gate status: ${status}"
                        }
                    } else {
                        error "Failed to retrieve quality gate status from SonarQube."
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
