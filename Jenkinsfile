#!groovy

properties(
    [
        [$class: 'BuildDiscarderProperty', strategy:
          [$class: 'LogRotator', artifactDaysToKeepStr: '14', artifactNumToKeepStr: '5', daysToKeepStr: '30', numToKeepStr: '60']],
        pipelineTriggers(
          [
              pollSCM('H/15 * * * *'),
              cron('@daily'),
          ]
        )
    ]
)
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'npm install -g grunt-cli@0.1.x karma@0.8.x'
                sh 'cd server && npm install --quiet'
                sh 'node server.js &'
                sh 'sleep 5'
                sh 'cd ../client && npm install --quiet'
                sh 'grunt release'
            }
        }
        stage('Test') {
            steps {
                sh 'cd server && grunt release'
                sh 'cd ../client && grunt release'
            }
        }

    }
}
