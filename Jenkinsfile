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
           
        stage('Quality Gate') {
            steps {
                timeout(time: 60, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Upload to Nexus') {
            steps {
                def version = '13'
                def projectName = 'python-app'
                def artifactFile = "${projectName}-${version}.tar.gz"
                def groupPath = 'com/example/python-app'
                def nexusUrl = 'http://nexus:8081'
                def repository = 'python-app'
                def credentialsId = 'nexus-creds'

                
                 // Compress project source
                        sh "tar -czf ${artifactFile} sources/*.json"

                        // Get credentials from Jenkins (username and password)
                        withCredentials([usernamePassword(credentialsId: credentialsId, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                            def uploadUrl = "${nexusUrl}/repository/${repository}/${groupPath}/${version}/${artifactFile}"
                            
                            // Upload using curl
                            sh """
                                curl -u $NEXUS_USER:$NEXUS_PASS --upload-file ${artifactFile} ${uploadUrl}

                            """
                        }
            }
        }
    }
}
                        
            
    



        
                
        
    

    

    
        


                    