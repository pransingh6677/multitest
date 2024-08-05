pipeline { 
    agent any

    environment {
        // Define environment variables with default values
        PROD_APP_DIR = '/var/www/html/indiapollapi-main-prod'
        TEST_APP_DIR = '/var/www/html/indiapollapi-main-test'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checkout the code from the branch in the SCM
                echo "Checked out code from branch ${env.BRANCH_NAME}"
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
                    branch 'main'
                    branch 'test'
                }
            }
            steps {
                script {
                    def appDir = env.BRANCH_NAME == 'main' ? env.PROD_APP_DIR : env.TEST_APP_DIR
                    def execCommand = "sudo chown -R azureuser:azureuser ${appDir}"
                    def configName = ''

                    if (env.BRANCH_NAME == 'main') {
                        echo "Changing ownership on production server"
                        configName = 'ProdServerConfig'
                    } else if (env.BRANCH_NAME == 'test') {
                        echo "Changing ownership on development server"
                        configName = 'TestServer2'
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

        stage('Deploy to Production(main)') {
            when {
                branch 'main' // Deploy only for the main branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to production server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'ProdServerConfig',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.PROD_APP_DIR} && sudo bash deploy.sh",
                            execTimeout: 1800000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: env.PROD_APP_DIR,
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

        stage('Deploy to Development(test)') {
            when {
                branch 'test' // Deploy only for the test branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to development server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.TEST_APP_DIR} && sudo bash deploy.sh",
                            execTimeout: 1800000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: env.TEST_APP_DIR,
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
