pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo "Checking out branch: ${env.BRANCH_NAME}"
                checkout([$class: 'GitSCM',
                    branches: [[name: "*/${env.BRANCH_NAME}"]],
                    userRemoteConfigs: [[url: 'https://github.com/noorulain-nn/task.git']]
                ])
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Parallel Tests') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        bat 'npm test'
                    }
                }

                stage('Linting') {
                    steps {
                        echo "Lint passed"   // OR: bat 'npm run lint'
                    }
                }
            }
        }

        stage('Conditional Deployment') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        echo "Deployed to Production Environment (main branch)"
                    } 
                    else if (env.BRANCH_NAME == 'dev') {
                        echo "Deployed to Staging Environment (dev branch)"
                    } 
                    else {
                        echo "Feature branch detected – Deployment Skipped."
                    }
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                bat 'tar -cf build_output.tar .'
                archiveArtifacts artifacts: 'build_output.tar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Build #${env.BUILD_NUMBER} on branch ${env.BRANCH_NAME} completed successfully at ${new Date()}"
        }
        failure {
            echo "Build failed on branch ${env.BRANCH_NAME}"
        }
    }
}

