def gv
def env

pipeline {
    agent any
    parameters {
                    choice(name:'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'version to deploy on prod')
                    booleanParam(name:'executeTests', defaultValue: true  , description: 'run test suite')
                }
    stages {
        stage('Init-Stage') {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage('Build-Stage') {
            steps {
                script {
                    gv.buildApp()
                }
            }
        }
        stage('Test-Stage') {
            when {
                    expression {
                          params.executeTests
                    }
                }
            steps {
                script {
                    gv.testApp()
                }
            }
        }
        stage('Deploy-Stage') {
            steps {
                script {
                    env.ENV = input message: "Select the environment to deploy to", ok: "Done", parameters [choice(name:'ONE', choices: ['dev', 'staging', 'prod'], description: 'environment to deploy to')]
                    gv.deployApp()
                    echo "Deploying to ${ENV}"
                }
            }
        }
    }
}
