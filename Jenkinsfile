pipeline {
  agent any
  environment {
    APPSYSID = '4f96ddd11b901e503e6ccae2604bcb1a'
    BRANCH = "${BRANCH_NAME}"
    //SN_DEV_CREDENTIALS = 'Servicenow1'
    //SN_QA_CREDENTIALS = 'Servicenow2'
    AUTOMATION_ENV_1_CREDENTIALS = 'Servicenow1'
    AUTOMATION_ENV_2_CREDENTIALS = 'Servicenow2'
    //DEVENV = 'https://dev253989.service-now.com/'
    //TESTENV = 'https://dev272155.service-now.com/'
    AUTOMATION_ENV_1 = 'https://brightspeedtsmqa1.service-now.com/'
    AUTOMATION_ENV_2 = 'https://brightspeedtsmqa2.service-now.com/'
    //TESTSUITEID = 'b1ae55eedb541410874fccd8139619fb'
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
        snInstallApp(credentialsId: "${SN_QA_CREDENTIALS}", url: "${TESTENV}", appSysId: "${APPSYSID}")
        snRunTestSuite(credentialsId: "${SN_QA_CREDENTIALS}", url: "${TESTENV}", testSuiteSysId: "${TESTSUITEID}", withResults: true)
      }
    }
    stage('Deploy') {
      when {
        branch 'master'
      }
      steps {
        snInstallApp(credentialsId: "${AUTOMATION_ENV_2_CREDENTIALS}", url: "${AUTOMATION_ENV_2}", appSysId: "${APPSYSID}")
      }
    }
        stage('Rollback')
    {
      when {
        branch 'rollback'
      }
      steps
      {
        snRollback(url: "${AUTOMATION_ENV_2}",credentialsId: "${AUTOMATION_ENV_2_CREDENTIALS}",versionSysId: "${TARGET_VERSION}")
      }
    }
  }
}

