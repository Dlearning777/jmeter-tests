pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        RESULTS_FILE = "result.jtl"
        TEST_PLAN = "perf-test.jmx"
        PATH = "/usr/bin:/bin:/usr/sbin:/sbin:/opt/homebrew/bin:/opt/homebrew/sbin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Dlearning777/jmeter-tests.git', branch: 'main'
            }
        }

        stage('Run JMeter Test') {
            steps {
                script {
                    sh """
                    #!/bin/bash
                    echo "Cleaning old reports..."
                    rm -rf ${REPORT_DIR}

                    echo "Running JMeter performance test..."
                    jmeter -n -t ${TEST_PLAN} -l ${RESULTS_FILE} -e -o ${REPORT_DIR}
                    """
                }
            }
        }

        stage('Publish JMeter Report') {
            steps {
                publishHTML (target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: 'JMeter Performance Report'
                ])
            }
        }
    }
    stages {
        // your stages (checkout, run JMeter, publishHTML)
    }
    post {
        success {
            emailext(
                subject: "JMeter Test Report - Build #${env.BUILD_NUMBER}",
                body: """
                Hello Team,<br><br>
                The latest JMeter performance test has completed successfully.<br>
                <b>Summary:</b><br>
                - Build: #${env.BUILD_NUMBER}<br>
                - Status: SUCCESS<br><br>
                You can view the detailed report <a href="${env.BUILD_URL}JMeter_20Performance_20Report/">here</a>.<br><br>
                Regards,<br>
                Jenkins
                """,
                mimeType: 'text/html',
                to: 'learningdon6@gmail.com'
            )
        }
        failure {
            emailext(
                subject: "JMeter Test Report - Build #${env.BUILD_NUMBER} FAILED",
                body: "The JMeter test failed. Please check the Jenkins logs.",
                to: 'learningdon6@gmail.com'
            )
        }
    }


    post {
        always {
            archiveArtifacts artifacts: '**/*.jtl', fingerprint: true
        }
    }
}
