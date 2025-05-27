pipeline {
    agent any


    stages {

        stage('install  os deps') {
            steps {
                sh 'apt-get update && apt-get install npm -y'
            }
        }

        stage('install node deps') {
            steps {
                sh 'npm install'
            }
        }

        stage('Install Test Reporter') {
            steps {
                sh 'npm install --save-dev jest-junit'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test -- --ci --reporters=default --reporters=jest-junit'
            }
        }

        stage('Publish Test Results') {
            steps {
                junit 'junit.xml'
            }
        }
           stage("CodeScanning"){
            environment {
               SONAR_HOME = tool 'sonar-scan'
            }
            steps {
                withSonarQubeEnv('SonarServer') {
              
                    sh '''${SONAR_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=myPETC \
                    -Dsonar.projectName=mypetclinc \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.exclusions=src/test/java/****/*.java \
                    -Dsonar.analysis.mode=publish \
                    -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}
                    '''
                }
            }
           }
    }
}
    

    
        


                    