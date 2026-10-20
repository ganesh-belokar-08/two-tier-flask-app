pipeline {
    agent any

    environment {
        IMAGE_NAME = "bravo08/two-tier-flask-app"
    }

    stages {
        stage("Code Clone") {
            steps {
                echo "Cloning application repository..."
                git url: "https://github.com/ganesh-belokar-08/two-tier-flask-app.git",
                    branch: "main"
            }
        }

        stage("Build") {
            steps {
                echo "Building Docker image via compose..."
                sh 'docker compose build flask-app'
                sh 'docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

        stage("Test") {
            steps {
                echo "Checking Docker image..."
                sh 'docker image inspect ${IMAGE_NAME}:latest'
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCreds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push ${IMAGE_NAME}:latest'
                    sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying..."
                sh 'docker compose up -d --no-deps --force-recreate flask-app'
                sh 'docker image prune -f'
            }
        }

        stage("Verify") {
            steps {
                sh 'sleep 15'
                sh 'docker compose ps'
                sh 'docker compose logs --tail=50 flask-app'
                sh 'docker inspect flask-app --format="{{.Config.Image}}"'
                sh 'curl -f http://localhost:5000/health'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
            echo "Pipeline execution finished."
        }
        success { echo "CI/CD pipeline completed successfully!" }
        failure { echo "Pipeline failed. Check the stage logs." }
    }
}
