pipeline {
    agent any
    environment {
        BRANCH = "${BRANCH_NAME}"
        APPSYSID = '00f35c601b2b9410fe0165f8bc4bcb06'
        CREDENTIALS = '7b4ca59e-8486-486c-895e-f044a5297447'
        DEVENV = 'https://devinstance.service-now.com/'
        TESTENV = 'https://testinstance.service-now.com/'
        PRODENV = 'https://prodinstance.service-now.com/'
        TESTSUITEID = 'b1ae55eedb541410874fccd8139619fb'
    }
    stages {
        stage('Build') {
            when {
                    branch 'developer'
            }
            steps {
                snApplyChanges(appSysId: "${APPSYSID}", branchName: "${BRANCH}", url: "${DEVENV}", credentialsId: "${CREDENTIALS}")
                snPublishApp(credentialsId: "${CREDENTIALS}", url: "${DEVENV}", appSysId: "${APPSYSID}",
                        isAppCustomization: true, obtainVersionAutomatically: true, incrementBy: 2)
            }
        }
        stage('Install') {
             when {
                    branch 'test'
            }
            steps {
                snInstallApp(credentialsId: "${CREDENTIALS}", url: "${TESTENV}", appSysId: "${APPSYSID}", baseAppAutoUpgrade: false)
                snRunTestSuite(credentialsId: "${CREDENTIALS}", url: "${TESTENV}", testSuiteSysId: "${TESTSUITEID}", withResults: true)
            }
        }
        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                snInstallApp(credentialsId: "${CREDENTIALS}", url: "${PRODENV}", appSysId: "${APPSYSID}", baseAppAutoUpgrade: false)
            }
        }
    }
}
