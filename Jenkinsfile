pipeline {
    agent any
    tools {
        maven "maven-3.9"
    }
    stages {
        stage('build jar') {
            steps {
                script {
                    echo "building the application..."
                    sh 'mvn package'
                }
            }
        }
        stage('build image') {
            steps {
                script {
                    echo "building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', usernameVariable: 'USERNAME', passwordVariable: 'PWD')]) {
                        sh 'docker build -t uba31/demo-app:jma-2.1 .'
                        sh 'echo $PWD | docker login -u $USERNAME --password-stdin'
                        sh 'docker push uba31/demo-app:jma-2.1'
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo "building the application..."
                }
            }
        }
    }
}
