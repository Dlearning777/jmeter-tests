pipeline {
    agent any

    environment {
        REPORT_DIR = "report"
        RESULTS_FILE = "result.jtl"
        TEST_PLAN = "influxdb-jmeter-testplan.jmx"
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
                        echo "Cleaning old reports and result file..."
                rm -rf ${REPORT_DIR}
                rm -f ${RESULTS_FILE}

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

    post {
        success {
            emailext(
                subject: "✅ JMeter Test Report - Build #${env.BUILD_NUMBER}",
                body: """
                Hello Team,<br><br>
                The latest JMeter performance test completed successfully.<br><br>
                <b>Details:</b><br>
                - Build: #${env.BUILD_NUMBER}<br>
                - Status: <b>SUCCESS</b><br>
                - Report: <a href="${env.BUILD_URL}JMeter_20Performance_20Report/">Click Here</a><br><br>
                Regards,<br>
                Jenkins
                """,
                mimeType: 'text/html',
                to: 'learningdon6@gmail.com'
            )
        }
        failure {
            emailext(
                subject: "❌ JMeter Test Report - Build #${env.BUILD_NUMBER} FAILED",
                body: """
                Hello Team,<br><br>
                The JMeter test <b>FAILED</b>.<br><br>
                Please check Jenkins build logs for details.<br><br>
                Regards,<br>
                Jenkins
                """,
                mimeType: 'text/html',
                to: 'learningdon6@gmail.com'
            )
        }
        always {
            archiveArtifacts artifacts: '**/*.jtl', fingerprint: true
        }
    }
}
