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
        //execute deployment in parallel.
        parallel(
                'us-west-2-lab': {
                    deployToStockyard(
                            serviceName: serviceName,
                            stage: "alpha",
                            substrate: "us-west-2-lab",
                            buildVersion: env.VERSION,
                            healthCheck: true,
                            postDeploymentSleepMs: 1000
                    )
                }
        )
    }
  }
}