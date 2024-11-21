pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('repo_PAT')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
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
                updateGitHubStatus('SUCCESS', 'Build and coverage passed')
            }
        }

        failure {
            script {
                updateGitHubStatus('FAILURE', 'Build or coverage failed')
            }
        }
    }
}

def updateGitHubStatus(String state, String description) {
    def context = "ci-jenkins"
    def commitSha = env.GIT_COMMIT

    withCredentials([string(credentialsId: 'repot_PAT_secret', variable: 'GITHUB_TOKEN')]) {
        def url = "https://api.github.com/repos/OhhTuRnz/TestFJCL/statuses/${commitSha}"
        def data = """
        {
            "state": "${state}",
            "description": "${description}",
            "context": "${context}"
        }
        """
        // sh "curl -X POST -H 'Authorization: token ${GITHUB_TOKEN}' -d '${data}' ${url}"
    }
}