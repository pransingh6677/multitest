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
                    if (branchName.startsWith('sprint1/')) {
                        echo "Running feature branch pipeline"
                        load 'Jenkinsfile.sprint1'
                    } else if (branchName.startsWith('test/')) {
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
