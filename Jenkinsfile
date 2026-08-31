pipeline {
    agent any

    environment {
        IMAGE_NAME       = 'patho'
        CONTAINER_NAME   = 'patho-container'
        // Notification targets - apne hisaab se badlein
        EMAIL_RECIPIENTS = 'shg975@gmail.com'
        // SLACK_CHANNEL    = '#devops-alerts'
    }

    stages {

        // Stage 1: Source code checkout
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/SatvikHGupta/Pathalogy.git'
            }
        }

        // Stage 2: SCA - dependency vulnerabilities (OWASP)
        stage('OWASP Dependency Check') {
            steps {
                withCredentials([string(credentialsId: 'nvd-api-key',
                                        variable: 'NVD_API_KEY')]) {
                    dependencyCheck(
                        additionalArguments: '''
                            --scan ./
                            --disableYarnAudit
                            --disableNodeAudit
                            --format XML
                            --format HTML
                            --nvdApiKey $NVD_API_KEY
                        ''',
                        odcInstallation: 'DP-Check'
                    )
                }
            }
        }

        // Stage 3: SAST - code quality (SonarQube)
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    script {
                        def scannerHome = tool 'sonar-scanner'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=Pathalogy \
                              -Dsonar.projectName="Pathalogy" \
                              -Dsonar.sources=. \
                              -Dsonar.exclusions="**/node_modules/**,**/dist/**,**/build/**" \
                              -Dsonar.sourceEncoding=UTF-8
                        """
                    }
                }
            }
        }

        // Stage 4: Filesystem security scan (Trivy)
        stage('Trivy Filesystem Scan') {
            steps {
                sh '''
                    trivy fs . \
                      --severity HIGH,CRITICAL \
                      --format table \
                      -o trivy-fs-report.txt
                '''
            }
        }

        // Stage 5: Build container image
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        // Stage 6: Image security scan (Trivy)
        stage('Trivy Docker Image Scan') {
            steps {
                sh '''
                    trivy image \
                      --severity HIGH,CRITICAL \
                      --format table \
                      -o trivy-image-report.txt \
                      ${IMAGE_NAME}
                '''
            }
        }

        // Stage 7: Deploy the container
        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d \
                      -p 80:80 \
                      --name ${CONTAINER_NAME} \
                      ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        // Har build ke baad reports archive karo
        always {
            archiveArtifacts(
                artifacts: 'trivy-*.txt,**/dependency-check-report.*',
                allowEmptyArchive: true
            )
            dependencyCheckPublisher(pattern: '**/dependency-check-report.xml')
        }

        // Build PASS - Email + Slack (success)
        success {
            // slackSend(
            //     channel: env.SLACK_CHANNEL,
            //     color: 'good',
            //     message: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} deployed " +
            //              "(<${env.BUILD_URL}|open build>)"
            // )
            emailext(
                to: env.EMAIL_RECIPIENTS,
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                attachmentsPattern: 'trivy-*.txt',
                body: "<h3>Build Successful</h3>" +
                      "<p>Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}<br/>" +
                      "URL: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>" +
                      "<p>Trivy reports attached.</p>"
            )
        }

        // Build FAIL - Email + Slack (failure)
        failure {
            // slackSend(
            //     channel: env.SLACK_CHANNEL,
            //     color: 'danger',
            //     message: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} failed " +
            //              "(<${env.BUILD_URL}console|check logs>)"
            // )
            emailext(
                to: env.EMAIL_RECIPIENTS,
                subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                body: "<h3>Build Failed</h3>" +
                      "<p>Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}<br/>" +
                      "Console: <a href='${env.BUILD_URL}console'>view logs</a></p>"
            )
        }
    }
}