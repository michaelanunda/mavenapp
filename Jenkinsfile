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
        stage('commit version update') {
            steps {
                script {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USERNAME', passwordVariable: 'TOKEN')]) {
                    sh 'git config --global user.email "michaelanunda@gmail.com"'
                    //sh 'git config --global user.name "michaelanunda"'

                    sh 'git status'
                    sh 'git branch'
                    sh 'git config --list'

                    sh 'git remote set-url origin https://${USERNAME}:${TOKEN}@github.com/michaelanunda/mavenapp.git'
                    sh 'git add .'
                    sh 'git commit -m "ci: version bump of pom.xml file to match Jenkins"'
                    sh "git push origin HEAD:jenkins-jobs"
                  } 
                }
            }
        }
    }
}
