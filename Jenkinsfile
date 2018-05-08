pipeline {
  agent any
  stages {
    stage('Stage1') {
      steps {
        echo 'Stage 1 started'
        sleep 2
        input(message: 'Enter stage:', id: 'stage', ok: 'ok', submitter: 'Submitter', submitterParameter: 'SubmitterParameter')
      }
    }

        stage('Example') {
            input {
                message "Should we continue?"
                ok "Yes, we should."
                submitter "alice,bob"
                parameters {
                    string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
                }
            }
            steps {
                echo "Hello, ${PERSON}, nice to meet you."
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