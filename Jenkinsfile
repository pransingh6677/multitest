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
                    if (branchName.startsWith('feature/')) {
                        echo "Running feature branch pipeline"
                        load 'Jenkinsfile.feature'
                    } else if (branchName.startsWith('hotfix/')) {
                        echo "Running hotfix branch pipeline"
                        load 'Jenkinsfile.hotfix'
                    } else {
                        echo "Running default pipeline"
                        load 'Jenkinsfile.default'
                    }
                }
            }
        }
    }
}
