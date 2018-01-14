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
                sh 'cd ./client && npm install --quiet && grunt build'
            }
        }
        stage('Test') {
            steps {
            //Fix for nodeunit failing
                sh 'npm update grunt-contrib-nodeunit -S'
                sh 'cd server && grunt default timestamp'
                sh 'node ./server/server.js &'
                sh 'sleep 5'
                sh 'cd ./client && grunt release --force'
            }
        }
        stage('Release') {
            steps {
                sh 'git checkout -b rc-"$BUILD_NUMBER"'
                sh 'git push origin -u rc-"$BUILD_NUMBER"'
            }
        }
    }
}
