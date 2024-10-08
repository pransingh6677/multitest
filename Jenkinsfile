pipeline {
    agent any  

    environment {
        // Define environment variables with default values 
        PROD_APP_DIR = '/var/www/html/indiapollapi-main-prod'
        TEST_APP_DIR = '/var/www/html/indiapollapi-main-test'
        DEV_APP_DIR = '/var/www/html/indiapollapi-main-dev' // Added for the dev branch
        BACKUP_DIR = '/var/backupProd' // Ensure this directory exists on the server
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

        stage('Backup Production') {
            when {
                branch 'main' // Run only for the main branch
            }
            steps {
                script {
                    echo "Taking backup of production server before deployment"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2',
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
            branch 'sprint-*'
        }
    }
    steps {
        script {
            def appDir = ''
            def execCommand = ''
            def configName = ''

            if (env.BRANCH_NAME == 'main') {
                echo "Changing ownership on production server"
                appDir = env.PROD_APP_DIR
                execCommand = "sudo chown -R jenkins:jenkins ${appDir}"
                configName = 'TestServer2'
            } else if (env.BRANCH_NAME == 'test') {
                echo "Changing ownership on testing server"
                appDir = env.TEST_APP_DIR
                execCommand = "sudo chown -R azureuser:azureuser ${appDir}"
                configName = 'TestServer2'
            } else if (env.BRANCH_NAME.startsWith('sprint-')) {
                echo "Changing ownership on development server"
                appDir = env.DEV_APP_DIR
                execCommand = "sudo chown -R azureuser:azureuser ${appDir}"
                configName = 'TestServer2'
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


        

        stage('Deploy to Production') {
            when {
                branch 'main' // Deploy only for the main branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to production server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2',
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
                branch 'test' // Deploy only for the test branch
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

        stage('Deploy to Dev') {
            when {
                branch 'sprint-*' // Deploy only for the dev branch
            }
            steps {
                script {
                    echo "Deploying branch ${env.BRANCH_NAME} to development server"
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2', // Ensure this config is defined
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
