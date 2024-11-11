pipeline {
    agent any  // This allows the pipeline to run on any available Jenkins agent.

    environment {
        DOCKER_IMAGE = "goresameer18127/java-app:latest"  // Defines the Docker image name and tag
        REPO_URL = "https://github.com/S08gore/Shopping-Cart.git"  // Sets the URL for your GitHub repository
    }

    stages {
        // Each stage represents a step in the CI/CD process.

        stage('Clone Repository') {
            steps {
                // Clones the repository from GitHub to the Jenkins workspace.
                git url: "${REPO_URL}", branch: 'main'
            }
        }
        
        stage('Build') {
            steps {
                // Compiles and packages the Java application.
                // Here, `mvnw` is the Maven wrapper command. Adjust to `./gradlew` if you use Gradle.
                script {
                    sh './mvnw clean package'
                }
            }
        }

        stage('Test') {
            steps {
                // Executes tests for the Java application.
                // Also uses `mvnw` here; adjust as needed for Gradle.
                script {
                    sh './mvnw test'
                }
            }
        }

        stage('Dockerize') {
            steps {
                // Builds a Docker image for the application.
                script {
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                // Logs into DockerHub and pushes the Docker image.
                // `withCredentials` injects DockerHub credentials for the push command.
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // Deploys the application by running the Docker container.
                // This example deploys locally on port 8080.
                script {
                    sh 'docker run -d -p 8080:8080 ${DOCKER_IMAGE}'
                }
            }
        }
    }
    
    post {
        // `post` blocks handle the result of the pipeline.
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
