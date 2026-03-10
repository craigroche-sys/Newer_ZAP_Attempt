
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
            timeout: 1000,
            zapHome: "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\ZAP_Test_4\\ZAP\\Zed Attack Proxy",
            allowedHosts: ['github.com',
                          '127.0.0.1',
                          'localhost',
                          'qa2022-30246.sotiqa.com'],
            sessionPath: "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\ZAP_Test_4\\Sessions\\pipedream.session"
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

    
stage('Explore & Attack with ZAP') {
  steps {
    script {
      def target = 'https://qa2022-30246.sotiqa.com/*'
      
      runZapCrawler(
        host: 'https://qa2022-30246.sotiqa.com/*n',
        recurse: true,
        subtreeOnly: false,
        contextName: 'pipedream-context'
      )

      runZapAttack(
        inScopeOnly: true,
        recurse: true
      )
    }
  }
}

    
    stage('Archive ZAP Report'){
      steps{
        script{
          archiveZap(
            failAllAlerts: 0,
            failHighAlerts: 1,
            failMediumAlerts: 6,
            failLowAlerts: 5,
            keepAlive: false
            )
        }
      }
    }   
  }
 post{
    always{
      archiveArtifacts artifacts: 'ZAP_Report.html', fingerprint: true, allowEmptyArchive: true
    }
  }
}



