pipeline {
    agent any
    environment {
        BRANCH = 'master'
        HELM_STATE_GIT_REPO = 'github.com/infracloudio/app-mono-helmstate.git'
        HELM_STATE_REPO = 'app-mono-helmstate'
        GITHUB_HOOK_SECRET = "github-webhook-token"
        GIT = credentials('github-credentials')
    }

    stages {
        stage('configure webook') {
            steps {
                script {
                    setupWebhook()
	        }
	    }
        }

        stage('Find app name to deploy') {
            steps {
                script {
                    if (REF != "") {
                        VALUESFILE = sh(returnStdout: true, script:'git show --name-only --pretty=""')
                        env.APP = VALUESFILE.split('/')[0] // TODO: change index and add error handling
                    }
                }
                echo "Changes in:${VALUESFILE}"
                echo "application to build:${APP}"
            }
        }

    }
    post {
        cleanup {
            deleteDir()
        }
    }
}

def setupWebhook() {
    properties([
        pipelineTriggers([
            [$class: 'GenericTrigger',
                genericVariables: [
                    [key: 'REF', value: '$.ref'],
                ],
                causeString: 'Triggered on github push',
                token: env.GITHUB_HOOK_SECRET,
                printContributedVariables: true,
                printPostContent: true,
            ]
        ])
    ])
}
