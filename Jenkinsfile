pipeline {
  agent any
  stages {
    stage('Stage1') {
      steps {
        echo 'Stage 1 started'
        sleep 2
      }
    }

        stage('Run Script') {
            steps {
                echo 'Hello World'
                script {
                    def browsers = ['chrome', 'firefox']
                    for (int i = 0; i < browsers.size(); ++i) {
                        echo "Testing the ${browsers[i]} browser"
                    }
                }
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
    stage('Get Input') {
      input {
        message 'Should we continue?'
        id 'Yes, we should.'
        parameters {
          string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        }
      }
      steps {
        echo "Hello, ${PERSON}, nice to meet you."
      }
    }

  }
}
