pipeline {
  agent any
  stages {
    stage('Stage1') {
      steps {
        echo 'Stage 1 started'
        sleep 15
      }
    }
    stage('Deploy alpha') {
      parallel {
        stage('us-west-2-lab') {
          steps {
            echo 'Stage: Deploy alpha us-west-2-lab started'
          }
        }
      }
    }
  }
}