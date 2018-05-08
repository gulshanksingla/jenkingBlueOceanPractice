#!groovy

//Documentation: https://confluence/display/ECT/Egencia+Jenkins+Blue+Ocean

import groovy.transform.Field

def serviceName = JOB_NAME.replaceAll(/-pipeline.*/, "")
def slackChannel = "#ege-jenkins-general"
def listOfApprovers = "egencia-tech-leadership"
def serviceOwner = "serviceOwner" 
def branch = env.BRANCH_NAME
def MASTER_BRANCH = "master"
@Field def commit
@Field def buildState

try {
    if (branch == MASTER_BRANCH) {
        //runs  maven build to yeild an artifact and/or a docker image and returns the build version.
        env.VERSION = egenciaBuild(serviceName: serviceName, uploadAssets: false, slackChannel: slackChannel, profiles: "ok,test")

        if (!platformCompliance("${serviceName}")) {
            def inputMessage = "Platform Compliance Failed.  Please request sponser approval from your team to continue! List of approvers: ${listOfApprovers}"
            slackSend channel: "${slackChannel}", color: 'danger', message: "@here [CUMULUS] <${env.RUN_DISPLAY_URL}|${env.JOB_NAME}:${env.BUILD_NUMBER}> ${inputMessage}. :disappointed:"
            timeout(time: 1, unit: 'MINUTES') {
                def userInput = input(id: 'userInput', message: inputMessage, parameters: [
                        [$class: 'TextParameterDefinition', defaultValue: '', submitter: "${listOfApprovers}", submitterParameter: "submitter", description: 'Description or Reason for promoting', name: 'desc']])
                echo 'User input was: ' + userInput
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

        //prompt for manual promotion
        stage('Continue?') {
            slackSend channel: "${slackChannel}", color: 'warning', message: "@here [CUMULUS] <${env.RUN_DISPLAY_URL}|${env.JOB_NAME}:${env.BUILD_NUMBER}> is waiting for manual approval to promote to BETA. :stopwatch:"
            def inputMessage = "do you want to proceed with pipeline promotion?"
            timeout(time: 30, unit: 'MINUTES') {
                def userInput = input(id: 'userInput', message: inputMessage, parameters: [
                        [$class: 'TextParameterDefinition', defaultValue: '', description: 'Description or Reason for promoting', name: 'desc']])
                echo 'User input was: ' + userInput
            }
        }

        //create servicenow change ticket
        env.CHANGE = serviceNowCreateChange(
                serviceName: "${serviceName}",
                coordinator: "${serviceOwner}",
                buildVersion: env.VERSION,
                stage: "beta"
        )

        stage('Deploy beta') {
            //execute deployment in parallel.
            parallel(
                    'us-west-2-lab': {
                        deployToStockyard(
                                serviceName: serviceName,
                                stage: "beta",
                                substrate: "us-west-2-lab",
                                buildVersion: env.VERSION,
                                healthCheck: true,
                                postDeploymentSleepMs: 1000
                        )
                    }
            )
        }

        //close servicenow change ticket
        serviceNowCloseChange(changeNumber: env.CHANGE)

        stage('Deploy gamma') {
            //execute deployment in parallel.
            parallel(
                    'us-west-2-prod': {
                        deployToStockyard(
                                serviceName: "${serviceName}",
                                stage: "gamma",
                                substrate: "us-west-2-prod",
                                buildVersion: env.VERSION,
                                healthCheck: true,
                                postDeploymentSleepMs: 1000
                        )
                    }
            )
        }

        //prompt for manual promotion
        stage('Continue?') {
            slackSend channel: "${slackChannel}", color: 'warning', message: "@here [CUMULUS] <${env.RUN_DISPLAY_URL}|${env.JOB_NAME}:${env.BUILD_NUMBER}> is waiting for manual approval to promote to PROD. :stopwatch:"
            def inputMessage = "do you want to proceed with pipeline promotion?"
            timeout(time: 30, unit: 'MINUTES') {
                def userInput = input(id: 'userInput', message: inputMessage, parameters: [
                        [$class: 'TextParameterDefinition', defaultValue: '', description: 'Description or Reason for promoting', name: 'desc']])
                echo 'User input was: ' + userInput
            }
        }

        //promote docker image to egencia-docker-prod.jfrog.io repository for production deployment.
        promoteImage serviceName: serviceName, buildVersion: env.VERSION

        env.CHANGE = serviceNowCreateChange(
                serviceName: "${serviceName}",
                coordinator: "${serviceOwner}",
                buildVersion: env.VERSION,
                stage: "prod"
        )

        stage('Deploy prod') {
            //execute deployment in parallel.
            parallel(
                    'us-west-2-prod': {
                        deployToStockyard(
                                serviceName: "${serviceName}",
                                stage: "prod",
                                substrate: "us-west-2-prod",
                                buildVersion: env.VERSION,
                                healthCheck: true,
                                postDeploymentSleepMs: 1000
                        )
                    }
            )
        }

        serviceNowCloseChange(changeNumber: env.CHANGE)
    } else {
        //runs pr builds
        egenciaPullRequestBuild(serviceName: serviceName)
    }
    postCompletionSuccess()
} catch (Exception error) {
    //marks the build as failure when there is an exception
    postCompletionFailed(error: error)
}

