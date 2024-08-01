pipeline {
    agent any
    stages {
        stage('Run Different Configurations') {
            steps {
                script {
                    def branchName = env.BRANCH_NAME
                    echo "Branch Name: ${branchName}"
                    
                    if (branchName == 'main') {
                        echo "Running main branch pipeline"
                        evaluate readFile('Jenkinsfile.main')
                    } else if (branchName == 'test') {
                        echo "Running test branch pipeline"
                        evaluate readFile('Jenkinsfile.test')
                    } else {
                        echo "Running default pipeline"
                        evaluate readFile('Jenkinsfile.default')
                    }
                }
            }
        }
    }
}
