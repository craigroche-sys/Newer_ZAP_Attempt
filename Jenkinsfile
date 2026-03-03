
pipeline {
  agent any

  tools {
    maven "Maven_3.9.12"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup') {
      steps {
        script {
          startZap(
            host: "127.0.0.1",
            port: 9091,
            timeout: 500,
            zapHome: "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\ZAP_Test_4\\ZAP\\Zed Attack Proxy",
            allowedHosts: ['github.com']
          )
        }
      }
    }

    stage('Build') {
      steps {
        // If you need a second repo, isolate it to avoid replacing your Jenkinsfile workspace
        dir('maven-src') {
          git url: 'https://github.com/craigroche-sys/Maven_Test.git', branch: 'main'
          bat 'mvn -Dmaven.test.failure.ignore=true clean package'
        }
      }
    }
  }

  // ✅ pipeline-level post must be OUTSIDE "stages"
  post {
    always {
      archiveZap(
        failAllAlerts:          false,
        failHighAlerts:         true,
        failMediumAlerts:       false,
        failLowAlerts:          false,
        falsePositivesFilePath: '',
        keepAlive:              false
      )
    }
  }
}

