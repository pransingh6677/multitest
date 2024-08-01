pipeline {
    agent any
    stages {
        // stage('Checkout') {
        //     steps {
        //         checkout scm
        //     }
        // }

        stage('Run Different Configurations') {
            steps {
                script {
                    def branchName = env.BRANCH_NAME
                    echo branchName
                    if (branchName.startsWith('main')) {
                        echo "Running feature branch pipeline"
                        load 'Jenkinsfile.main'
                    } else if (branchName.startsWith('test')) {
                        echo "Running hotfix branch pipeline"
                        load 'Jenkinsfile.test'
                    } else {
                        echo "Running default pipeline"
                        load 'Jenkinsfile.default'
                    }
                }
            }
        }
    }
}
