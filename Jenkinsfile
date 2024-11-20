pipeline {
    agent any

    environment {
        GITHUB_CREDS = 'github-credentials-id' // Jenkins credential ID for GitHub
        GITHUB_REPO = 'fjcl/TestFJCL'          // GitHub repository (e.g., 'user/repo')
        GITHUB_COMMIT = ''                     // Populated dynamically
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Get the commit hash
                    GITHUB_COMMIT = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
                // Checkout the code
                checkout scm
            }
        }
        stage('Set Pending Status') {
            steps {
                script {
                    githubNotify context: 'Build',
                                 status: 'PENDING',
                                 description: 'Build is in progress',
                                 repo: GITHUB_REPO,
                                 sha: GITHUB_COMMIT
                }
            }
        }
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
    }

    post {
        success {
            script {
                githubNotify context: 'Build',
                             status: 'SUCCESS',
                             description: 'Build and tests succeeded',
                             repo: GITHUB_REPO,
                             sha: GITHUB_COMMIT
            }
        }
        failure {
            script {
                githubNotify context: 'Build',
                             status: 'FAILURE',
                             description: 'Build or tests failed',
                             repo: GITHUB_REPO,
                             sha: GITHUB_COMMIT
            }
        }
        always {
            echo 'Build process completed'
        }
    }
}