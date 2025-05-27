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
        
         stage('Upload Artifact to Nexus') {
            steps {
                script {
                    // Extract version from pom.xml
                     def version = sh(
                        script: "./mvnw help:evaluate -Dexpression=project.version -q -DforceStdout",
                        returnStdout: true
                 ).trim()
                

                    // Choose repository based on version
                    def repository = version.contains('SNAPSHOT') ? 'maven-snapshots' : 'maven-releases'

                    // Derive artifactId and JAR file
                    def jarFile = sh(
                        script: "ls target/*.jar | grep simple-node-js-react-npm-app | head -n 1",
                        returnStdout: true
                    ).trim()

                    def artifactId = 'simple-node-js-react-npm-app' // This must match the filename and pom.xml <artifactId>

                    // Upload to Nexus
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: 'nexus:8081',
                        groupId: 'com.example',
                        version: version,
                        repository: repository,
                        credentialsId: 'nexus-creds',
                        artifacts: [
                            [
                                artifactId: artifactId,
                                classifier: '',
                                file: jarFile,
                                type: 'jar'
                            ]
                        ]
                    )
                }
            }
         }
        
    }
}
    

    
        


                    