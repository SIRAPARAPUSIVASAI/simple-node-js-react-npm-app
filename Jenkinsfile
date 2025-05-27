pipeline {
    agent any


    stages {

        stage('install deps') {
            steps {
                sh 'apt-get update && apt-get install npm -y'
            }
        }
        stage('Build') {
            steps {
                sh 'npm build'
            }
        }
    }
}
