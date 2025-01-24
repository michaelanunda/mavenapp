#!/user/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
               [$class: 'GitSCMSource', 
               remote: 'https://github.com/michaelanunda/jenkins-shared-library', 
               credentialsId: ''])

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
        stage('build jar') {
            steps {
                script {
                    buildJar()
                }
            }
        }
        stage('build and push image') {
            steps {
                script {
                    buildImage("uba31/demo-app", "jma-3.0")
                    dockerLogin()
                    dockerPush("uba31/demo-app", "jma-3.0")
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
    }
}
