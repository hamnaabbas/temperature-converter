pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build from')
        string(name: 'STUDENT_NAME', defaultValue: 'Your Name Here', description: 'Provide your name')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'prod'], description: 'Select environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run Jest Tests')
    }

    environment {
        APP_VERSION = "1.0.${BUILD_NUMBER}"
        MAINTAINER = "${params.STUDENT_NAME}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out branch: ${params.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build') {
            steps {
                bat '''
                    echo Simulating build...
                    if not exist build mkdir build
                    copy src\\*.js build
                    echo App version: %APP_VERSION% > build\\version.txt
                '''
            }
        }

        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                bat 'npm test'
            }
        }

        stage('Package') {
            steps {
                bat 'powershell Compress-Archive -Path build\\* -DestinationPath build_%APP_VERSION%.zip'
            }
        }

        stage('Deploy (Simulation)') {
            steps {
                echo "Deploying version ${APP_VERSION} to ${params.ENVIRONMENT}"
            }
        }
    }

    post {
        always {
            deleteDir()
        }
        success {
            echo "✅ Pipeline Succeeded — Version ${APP_VERSION}"
        }
        failure {
            echo "❌ Pipeline Failed"
        }
    }
}
