def gv
pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    stages {
        stage('init') {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
       stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "${version}-${env.BUILD_NUMBER}"
                }
            }
        }
        stage('build jar') {
            steps {
                script {
                    echo 'building the application...'
                    sh 'mvn clean package'
                }
            }
        }

        stage('build and push image') {
            steps {
                script {
                    echo "building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh 'docker build -t uba31/demo-app:${IMAGE_NAME} .'
                        sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                        sh 'docker push uba31/demo-app:${IMAGE_NAME}'
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }




/*
        stage('commit and push changes') {
            steps {
                script {
                    echo 'Committing and pushing the changes to pom.xml...'
                    
                    // Configure Git with Jenkins credentials
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GITHUB_USER', passwordVariable: 'GITHUB_TOKEN')]) {
                        sh """
                            git config user.name '${GITHUB_USER}'
                            git config user.email '${GITHUB_USER}@users.noreply.github.com'
                            git add .
                            git commit -m "Incremented version to ${env.IMAGE_NAME}"
                            git push https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/michaelanunda/mavenapp.git HEAD:jenkins-jobs
                        """
                    }
                }
            }
        }
*/


        
    }
}
