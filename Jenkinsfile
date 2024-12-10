pipeline {
    agent any
    parameters {
                    string(name:'VERSION_STRING', defaultValue: '', description: 'version to deploy on prod')
                    choice(name:'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'version to deploy on prod')
                    booleanParam(name:'executeTests', defaultValue: true  , description: 'run test suite')
                }
    stages {
        stage('Build-Stage') {
            steps {
                echo 'building the application'
            }
        }
        stage('Test-Stage') {
            when {
                    expression {
                          params.executeTests
                    }
                }
            steps {
                echo 'testing the application'
            }
        }
        stage('Deploy-Stage') {
            steps {
                echo 'deploying the application'
                echo "deploying version ${params.VERSION}"
            }
        }
    }
}
