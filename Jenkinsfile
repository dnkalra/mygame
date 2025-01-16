pipeline {
    agent any

    environment {
        // You can set environment variables here, if needed
        REGISTRY_URL = 'https://registry.hub.docker.com'
        DOCKER_IMAGE = 'dnkalra/mygame'
    }

    stages {
        stage('Cloning Git') {
            steps {
                // Let's make sure we have the repository cloned to our workspace
                // checkout scm
            }
        }

        stage('SAST') {
            steps {
                // Perform Static Application Security Testing
                build job: 'SECURITY-SAST-SNYK'
            }
        }

        stage('Build-and-Tag') {
            steps {
                // This builds the actual image; synonymous to "docker build"
                script {
                    app = docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Post-to-DockerHub') {
            steps {
                // Push the built image to DockerHub
                script {
                    docker.withRegistry(REGISTRY_URL, 'training_creds') {
                        app.push("latest")
                    }
                }
            }
        }

        stage('SECURITY-IMAGE-SCANNER') {
            steps {
                // Perform image security scanning
                build job: 'SECURITY-IMAGE-SCANNER-AQUAMICROSCANNER'
            }
        }

        stage('Pull-Image-Server') {
            steps {
                // Use Docker Compose to bring down and up the containers
                script {
                    sh "docker-compose down"
                    sh "docker-compose up -d"
                }
            }
        }

        stage('DAST') {
            steps {
                // Perform Dynamic Application Security Testing
                build job: 'SECURITY-DAST-OWASP_ZAP'
            }
        }
    }
}
