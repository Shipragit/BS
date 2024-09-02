pipeline {
  agent any
  environment {
    APPSYSID = '4f96ddd11b901e503e6ccae2604bcb1a'
    BRANCH = "${BRANCH_NAME}"
    AUTOMATION_ENV_1_CREDENTIALS = 'Servicenow1'
    AUTOMATION_ENV_2_CREDENTIALS = 'Servicenow2'
    AUTOMATION_ENV_1 = 'https://brightspeedtsmqa1.service-now.com/'
    AUTOMATION_ENV_2 = 'https://brightspeedtsmqa2.service-now.com/'
    TARGET_VERSION = '1.0.8'
  }
  stages {
    stage('Build') {
      when {
        branch 'development'
      }
      steps {
        snApplyChanges(appSysId: "${APPSYSID}", branchName: "${BRANCH}", url: "${AUTOMATION_ENV_1}", credentialsId: "${AUTOMATION_ENV_1_CREDENTIALS}")
        snPublishApp(credentialsId: "${AUTOMATION_ENV_1_CREDENTIALS}", appSysId: "${APPSYSID}", obtainVersionAutomatically: true, url: "${AUTOMATION_ENV_1}")
      }
    }
    stage('Test') {
      when {
        branch 'qa'
      }
      steps {
        snInstallApp(credentialsId: "${AUTOMATION_ENV_1_CREDENTIALS}", url: "${AUTOMATION_ENV_1}", appSysId: "${APPSYSID}")
        snRunTestSuite(credentialsId: "${AUTOMATION_ENV_1_CREDENTIALS}", url: "${AUTOMATION_ENV_1}", testSuiteSysId: "${TESTSUITEID}", withResults: true)
      }
    }
    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        snInstallApp(credentialsId: "${AUTOMATION_ENV_2_CREDENTIALS}", url: "${AUTOMATION_ENV_2}", appSysId: "${APPSYSID}")
      }
    }
    stage('Rollback') {
      when {
        branch 'rollback'
      }
      steps {
        script {
          def response = sh(script: """
            curl -X POST "${env.AUTOMATION_ENV_2}/api/now/table/your_table" \
            -u "${env.AUTOMATION_ENV_2}:${env.AUTOMATION_ENV_2_CREDENTIALS}" \
            -H 'Content-Type: application/json' \
            -d '{ "rollback": true, "appSysId": "${env.APPSYSID}" }'
          """, returnStdout: true).trim()

          echo "Rollback Response: ${response}"
        }
      }
    }
  }
}
