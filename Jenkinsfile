pipeline {
  agent any
  stages {
    stage('Stage1') {
      steps {
        echo 'Stage 1 started'
        sleep 15
      }
    }
    stage('Stage2') {
      steps {
        mail(subject: 'Testing', to: 'gulshanksingla@gmail.com', body: 'Testing BlueOceans')
      }
    }
  }
}