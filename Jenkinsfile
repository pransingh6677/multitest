pipeline { 
    agent any

    stages {
        stage('Changing Ownership to azureuser') {
            when {
                anyOf {
                    branch 'master'
                    branch 'dev'
                }
            }
            steps {
                script {
                    def execCommand = 'sudo chown -R azureuser:azureuser /var/www/html/indiapollapi-main'
                    if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to production server"
                        // Add deployment steps for production
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "Deploying to development server"
                        // Add deployment steps for development
                    } else {
                        echo "Unknown branch, skipping deployment"
                    }
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2', 
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
                        verbose: true // Set verbose to true for debugging
                    )])
                }
            }
        }

        stage('Deploying') {
            when {
                anyOf {
                    branch 'master'
                    branch 'dev'
                }
            }
            steps {
                script {
                    def execCommand = 'cd /var/www/html/indiapollapi-main && sudo bash deploy.sh'
                    if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to production server"
                        // Add deployment steps for production
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "Deploying to development server"
                        // Add deployment steps for development
                    } else {
                        echo "Unknown branch, skipping deployment"
                    }
                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'TestServer2', 
                        transfers: [sshTransfer(
                            cleanRemote: false, 
                            excludes: '', 
                            execCommand: execCommand, 
                            execTimeout: 1800000, 
                            flatten: false, 
                            makeEmptyDirs: false, 
                            noDefaultExcludes: false, 
                            patternSeparator: '[, ]+', 
                            remoteDirectory: '/var/www/html/indiapollapi-main', 
                            remoteDirectorySDF: false, 
                            removePrefix: '', 
                            sourceFiles: ''
                        )], 
                        usePromotionTimestamp: false, 
                        useWorkspaceInPromotion: false, 
                        verbose: true // Set verbose to true for debugging
                    )])
                }
            }
        }
    }
    
    post {
        always {
            // Delete workspace when build is done
            cleanWs(patterns: [[pattern: 'dependency-check-report.xml', type: 'EXCLUDE']]) // Jenkins Built-in-Feature for deleting the workspace and Cleaning env.
        }
    }
}
