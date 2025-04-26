pipeline {
    agent any

    environment {
        JMETER_HOME = "/opt/apache-jmeter-5.6.3" // adjust this based on your install
        REPORT_DIR = "report"
        RESULTS_FILE = "result.jtl"
        TEST_PLAN = "sample-api-test.jmx"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Dlearning777/jmeter-tests.git', branch: 'main'
            }
        }

        stage('Run JMeter Test') {
            steps {
                sh """
                rm -rf ${REPORT_DIR}
                ${JMETER_HOME}/bin/jmeter -n -t ${TEST_PLAN} -l ${RESULTS_FILE} -e -o ${REPORT_DIR}
                """
            }
        }

        stage('Publish Report') {
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
