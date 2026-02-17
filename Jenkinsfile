pipeline {
    agent {label "dev"};

    environment {
        IMAGE_NAME = "pipeline:v1"
    }

    stages {

        stage("Code") {
            steps {
                git url: "https://github.com/Nishant-Sharma-102/docker-deploy-javaScript-project.git", branch: "main"
            }
        }

        stage("Build") {
            steps {
                sh "docker build -t pipeline ."
            }
        }

        stage("Push") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds",
                    usernameVariable: "DOCKER_USER",
                    passwordVariable: "DOCKER_PASS"
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker tag pipeline:latest $DOCKER_USER/pipeline:v1
                    docker push $DOCKER_USER/pipeline:v1
                    '''
                }
            }
        }

        stage("Deploy") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds",
                    usernameVariable: "DOCKER_USER",
                    passwordVariable: "DOCKER_PASS"
                )]) {
                    sh '''
                    docker rm -f pipeline || true
                    docker run -d -p 80:80 --name pipeline $DOCKER_USER/pipeline:v1
                    '''
                }
            }
        }
    }
}
