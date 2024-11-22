pipeline {
    agent any

    stages {
        environment {
            PULL_REQUEST_ID = "${env.ghprbPullId}"  // Capture ghprbPullId as an environment variable
            PULL_REQUEST_LINK = "${env.ghprbPullLink}"  // Capture ghprbPullLink as an environment variable
        }

        stage('Checkout') {
            steps {
                checkout scm
                sh 'chmod +x gradlew'
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }

        stage('JaCoCo Test Coverage') {
            steps {
                sh './gradlew jacocoTestCoverageVerification'
            }
        }
    }

    post {
        success {
            script {
                setGitHubPullRequestStatus(context: 'CI-Jenkins', message: 'Build and coverage passed', state: 'SUCCESS')
            }
        }

        failure {
            script {
                setGitHubPullRequestStatus(context: 'CI-Jenkins', message: 'Build or coverage failed', state: 'FAILURE')
            }
        }
    }
}

def updateGitHubStatus(String state, String description) {
    def context = "ci-jenkins"
    def commitSha = env.GIT_COMMIT

    withCredentials([string(credentialsId: 'repo_fjcl_PAT_secret', variable: 'GITHUB_TOKEN')]) {
        def url = "https://api.github.com/repos/fjcl/TestFJCL/statuses/${commitSha}"
        def data = """
        {
            "state": "${state}",
            "description": "${description}",
            "context": "${context}"
        }
        """
        sh "curl -X POST -H 'Authorization: token ${GITHUB_TOKEN}' -d '${data}' ${url}"
    }
}