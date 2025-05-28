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
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "http://nexus:8081/",
                    groupId: 'com.example',
                    version: '0.1.0',
                    repository: 'my-node-app',
                    credentialsId: "nexus-creds}",
                    artifacts: [
                        [artifactId: 'my-node-app', classifier: '', file: 'dist/my-node-app.tar.gz', type: 'tar.gz']
                    ]
                )
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
                        
            
    



        
                
        
    

    

    
        


                    