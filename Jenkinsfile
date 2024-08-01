pipeline { 
    agent any

    stages {
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv(installationName: 'SonarQube Scanner') {
        //             sh """/opt/sonarqube/bin/sonar-scanner \
        //             -D sonar.projectBaseDir=/var/lib/jenkins/workspace/$JOB_NAME/ \
        //             -D sonar.exclusions=vendor/**,storage/** \
        //             -D sonar.projectKey=afms \
        //             -D sonar.sourceEncoding=UTF-8 \
        //             -Dsonar.host.url=http://sonarqube.proeffico.com \
        //             -Dsonar.login=sqa_769eb0ed2dd20f00c4e40f8d7c0bebb1e1b699dc"""
        //         }
        //     }
        // }
            
        // stage("Quality Gate") {
        //     steps {
        //       timeout(time: 1, unit: 'HOURS') {
        //         waitForQualityGate abortPipeline: true
        //       }
        //     }
        // }

        // stage("OWASP Scan") {
        //     steps {
        //         dependencyCheck additionalArguments: '', odcInstallation: 'dependency-check'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }

        stage('Changing Ownership to azureuser') {
            steps {
             if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to production server"
                        // Add deployment steps for production
                        sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'sudo chown -R azureuser:azureuser /var/www/html/indiapollapi-main', 
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
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "Deploying to development server"
                        // Add deployment steps for development
                        sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'sudo chown -R azureuser:azureuser /var/www/html/indiapollapi-main', 
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
                    } else {
                        echo "Unknown branch, skipping deployment"
                        sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'sudo chown -R azureuser:azureuser /var/www/html/indiapollapi-main', 
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
            steps {
            if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to production server"
                        // Add deployment steps for production
                        sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'cd /var/www/html/indiapollapi-main && sudo bash deploy.sh', 
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
                    } else if (env.BRANCH_NAME == 'dev') {
                        echo "Deploying to development server"
                        // Add deployment steps for development
                        sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'cd /var/www/html/indiapollapi-main && sudo bash deploy.sh', 
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
                    } else {
                        echo "Unknown branch, skipping deployment"
                    	sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'TestServer2', 
                    transfers: [sshTransfer(
                        cleanRemote: false, 
                        excludes: '', 
                        execCommand: 'cd /var/www/html/indiapollapi-main && sudo bash deploy.sh', 
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
