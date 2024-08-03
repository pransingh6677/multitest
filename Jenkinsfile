pipeline { 
    agent any

    environment {
        // Define any environment variables here
        APP_DIR = '/var/www/html/indiapollapi-main'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checkout the code from the branch in the SCM
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building branch ${env.BRANCH_NAME}"
                    // Insert build commands here
                    // Example: sh './build.sh'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Testing branch ${env.BRANCH_NAME}"
                    // Insert test commands here
                    // Example: sh './test.sh'
                }
            }
        }

        stage('Change Ownership') {
            when {
                anyOf {
                    branch 'master'
                    branch 'dev'
                }
            }
            steps {
                script {
                    def execCommand = "sudo chown -R azureuser:azureuser ${env.APP_DIR}"
                    def configName = ''
                    
                    if (env.BRANCH_NAME == 'master') {
                        echo "Changing ownership on production server"
                        configName = 'ProdServerConfig'
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "Changing ownership on development server"
                        configName = 'DevServerConfig'
                    } else {
                        echo "Unknown branch, skipping ownership change"
                    }

                    if (configName) {
                        sshPublisher(publishers: [sshPublisherDesc(
                            configName: configName,
                            transfers: [sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: execCommand,
                                execTimeout: 1800000,
                                flatten: false,
                                makeEmptyDirs: false,
                                noDefaultExcludes: false,
                                patternSeparator: '[, ]+',
                                remoteDirectory: '',
                                remoteDirectorySDF: false,
                                removePrefix: '',
                                sourceFiles: ''
                            )],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )])
                    }
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'master' // Deploy only for the master branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to production server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'ProdServerConfig',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.APP_DIR} && sudo bash deploy.sh",
                            execTimeout: 1800000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: env.APP_DIR,
                            remoteDirectorySDF: false,
                            removePrefix: '',
                            sourceFiles: ''
                        )],
                        usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false,
                        verbose: true
                    )])
                }
            }
        }

        stage('Deploy to Development') {
            when {
                branch 'dev' // Deploy only for the dev branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to development server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'DevServerConfig',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.APP_DIR} && sudo bash deploy.sh",
                            execTimeout: 1800000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: env.APP_DIR,
                            remoteDirectorySDF: false,
                            removePrefix: '',
                            sourceFiles: ''
                        )],
                        usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false,
                        verbose: true
                    )])
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs(patterns: [[pattern: 'dependency-check-report.xml', type: 'EXCLUDE']])
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
        unstable {
            echo 'Pipeline is unstable.'
        }
    }
}
