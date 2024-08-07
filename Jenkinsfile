pipeline {
    agent any

    environment {
        PROD_APP_DIR = '/var/www/html/indiapollapi-main-prod'
        TEST_APP_DIR = '/var/www/html/indiapollapi-main-test'
        DEV_APP_DIR = '/var/www/html/indiapollapi-main-dev'
        BACKUP_DIR = '/var/backupProd'
        SPRING_BRANCH_PREFIX = 'sprint-' // Prefix for sprint branches
        FALLBACK_CONFIG_NAME = 'TestServer2' // Default configuration for any other branch
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                script {
                    def branchName = sh(script: 'git name-rev --name-only HEAD', returnStdout: true).trim()
                    env.BRANCH_NAME = branchName
                    echo "Checked out code from branch ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building branch ${env.BRANCH_NAME}"
                    // Insert build commands here
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Testing branch ${env.BRANCH_NAME}"
                    // Insert test commands here
                }
            }
        }

        stage('Backup Production') {
            when {
                branch 'main'
            }
            steps {
                script {
                    echo "Taking backup of production server before deployment"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'ProductionServer',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "sudo tar -czf ${env.BACKUP_DIR}/backup_\$(date +%F_%T).tar.gz -C ${env.PROD_APP_DIR} .",
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

        stage('Change Ownership') {
            when {
                anyOf {
                    branch 'main'
                    branch 'test'
                    branch pattern: "${env.SPRING_BRANCH_PREFIX}.*" // Regex pattern for sprint branches
                    branch pattern: '.*' // Fallback for any other branch
                }
            }
            steps {
                script {
                    def appDir = ''
                    def execCommand = ''
                    def configName = ''

                    // Set appDir, execCommand, and configName based on the branch
                    if (env.BRANCH_NAME == 'main') {
                        echo "Changing ownership on production server"
                        appDir = env.PROD_APP_DIR
                        execCommand = "sudo chown -R jenkins:jenkins ${appDir}"
                        configName = 'ProductionServer'
                    } else if (env.BRANCH_NAME == 'test') {
                        echo "Changing ownership on testing server"
                        appDir = env.TEST_APP_DIR
                        execCommand = "sudo chown -R jenkins:jenkins ${appDir}"
                        configName = 'TestingServer'
                    } else if (env.BRANCH_NAME.startsWith(env.SPRING_BRANCH_PREFIX)) {
                        echo "Changing ownership on sprint server"
                        appDir = env.DEV_APP_DIR
                        execCommand = "sudo chown -R jenkins:jenkins ${appDir}"
                        configName = 'SprintServer'
                    } else {
                        echo "Changing ownership for fallback branch"
                        appDir = env.TEST_APP_DIR // Use the test directory for fallback
                        execCommand = "sudo chown -R jenkins:jenkins ${appDir}"
                        configName = env.FALLBACK_CONFIG_NAME
                    }

                    if (configName) {
                        echo "Using config: ${configName} for ownership change"
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

        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to production server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'ProductionServer',
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

        stage('Deploy to Testing') {
            when {
                branch 'test'
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to testing server"
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

        stage('Deploy to Sprint') {
            when {
                branch pattern: "${env.SPRING_BRANCH_PREFIX}.*" // Regex pattern for sprint branches
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to development server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'SprintServer',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.DEV_APP_DIR} && sudo bash deploy.sh",
                            execTimeout: 1800000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: env.DEV_APP_DIR,
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

        stage('Deploy to Fallback') {
            when {
                branch pattern: '.*' // Fallback for any other branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} using fallback configuration"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: env.FALLBACK_CONFIG_NAME,
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            excludes: '',
                            execCommand: "cd ${env.TEST_APP_DIR} && sudo bash deploy.sh", // Use TEST_APP_DIR for fallback
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
