pipeline { 
    agent any

    environment {
        echo ' Define environment variables if needed '
        //MY_ENV_VAR = 'some-value'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the version control system
                echo 'checkout scm..'
            }
        }

        stage('Build') {
            steps {
                // Execute build commands
                echo 'Building...'
                //sh './build-script.sh'  // For Unix-based systems
                // bat 'build-script.bat'  // For Windows systems
            }
        }

        stage('Test') {
            steps {
                // Execute test commands
                echo 'Testing...'
                //sh './test-script.sh'  // For Unix-based systems
                // bat 'test-script.bat'  // For Windows systems
            }
        }

        stage('Deploy') {
            steps {
                // Execute deployment commands
                echo 'Deploying...'
                //sh './deploy-script.sh'  // For Unix-based systems
                // bat 'deploy-script.bat'  // For Windows systems
            }
        }
    }

    post {
        always {
            // Actions to always run after the pipeline stages, e.g., cleanup
            echo 'Cleaning up...'
            //cleanWs()  // Clean workspace
        }
        success {
            // Actions to run if the pipeline succeeds
            echo 'Pipeline succeeded!'
        }
        failure {
            // Actions to run if the pipeline fails
            echo 'Pipeline failed!'
        }
    }
}
