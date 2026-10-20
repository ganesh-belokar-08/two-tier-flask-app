pipeline {
    agent any

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
            }
        }

        stage("Test") {
            steps {
                echo "Checking Docker image..."
                sh 'docker image inspect my-app:latest'
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying..."
                sh 'docker compose up -d --build --no-deps --force-recreate flask-app'
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
        success { echo "CI/CD pipeline completed successfully!" }
        failure { echo "Pipeline failed. Check the stage logs."  }
        always  { echo "Pipeline execution finished." }
    }
}