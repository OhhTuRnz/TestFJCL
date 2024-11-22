pipeline {
    agent any

    stages {
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
                updateGitHubStatus('success', 'Build and coverage passed')
            }
        }

        failure {
            script {
                updateGitHubStatus('failure', 'Build or coverage failed')
            }
        }
    }
}


def updateGitHubStatus(String state, String description) {
    def context = "ci/jenkins"
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