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
      steps {
        parallel(
                'us-west-2-lab': {
                    echo 'Stage: Deploy alpha us-west-2-lab started'
                }
        )
      }
    }
  }
}