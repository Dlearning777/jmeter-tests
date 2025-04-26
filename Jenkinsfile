pipeline {
    agent any

    environment {
        JMETER_HOME = "/opt/apache-jmeter-5.6.3" // Update if your JMeter is installed elsewhere
        REPORT_DIR = "report"
        RESULTS_FILE = "result.jtl"
        TEST_PLAN = "sample-api-test.jmx" // Your test file
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
                    ${JMETER_HOME}/bin/jmeter -n -t ${TEST_PLAN} -l ${RESULTS_FILE} -e -o ${REPORT_DIR}
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
        always {
            archiveArtifacts artifacts: '**/*.jtl', fingerprint: true
        }
    }
}
