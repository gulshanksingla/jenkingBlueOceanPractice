pipeline {
  agent any
  stages {
    stage('Stage1') {
      steps {
        echo 'Stage 1 started'
        sleep 2
        input(message: 'Enter stage:', id: 'stage')
      }
    }
    stage('Deploy alpha') {
      parallel {
        stage('us-west-2-lab') {
          steps {
            echo 'Stage: Deploy alpha us-west-2-lab started'
          }
        }
        stage('Print Env Variables') {
          steps {
            sh 'echo ${env}'
          }
        }
      }
    }
  }
}