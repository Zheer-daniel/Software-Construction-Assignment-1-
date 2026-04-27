// Software Construction — CI/CD pipeline (GitHub → Jenkins → Docker Hub)
// In Jenkins: create credentials "docker-hub-credentials" (Docker Hub username + access token)
// Job: Pipeline from SCM, point to this repo; enable GitHub hook for automatic builds

pipeline {
    agent any

    environment {
        // Match this to your Docker Hub repo (username/repository-name). You can override in the job’s “Environment variables”.
        DOCKER_HUB_REPO = 'zdqiu220275/ci-demo-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install and test') {
            steps {
                bat 'python -m pip install --upgrade pip'
                bat 'python -m pip install -r requirements-dev.txt'
                bat 'python -m pytest tests -v --tb=short'
            }
        }

        stage('Docker build and push') {
            steps {
                script {
                    def tag = "${env.BUILD_NUMBER}"
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        def image = docker.build("${DOCKER_HUB_REPO}:${tag}")
                        image.push()
                        image.push('latest')
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build ${env.BUILD_NUMBER} finished: image pushed to Docker Hub."
        }
        failure {
            echo "Build failed — check console output and test results."
        }
    }
}
