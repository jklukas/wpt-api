#!/usr/bin/env groovy

pipeline {
  agent none
  stages {
    stage('clone') {
       agent any
       steps {
         checkout([
           $class: 'GitSCM',
           branches: [[name: 'master']],
           extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'webpagetest-api']],
           userRemoteConfigs: [[url: 'https://github.com/marcelduran/webpagetest-api']]])
        }
    }
    stage('test') {
      agent {
        dockerfile { dir 'webpagetest-api' }
      }
      environment {
        WEB_PAGE_TEST = credentials('WEB_PAGE_TEST')
        WEBPAGETEST_SERVER = "https://${WEB_PAGE_TEST}@wpt-api.stage.mozaws.net/"
      }
      steps {
          sh '/usr/src/app/bin/webpagetest test "https://latest.dev.lcip.org/?service=sync&entrypoint=firstrun&context=fx_desktop_v3" -l "us-east-1:Firefox" -r 9 --first --poll --reporter json > fxa-homepage.json'
      }
      post {
        always {
          archiveArtifacts 'fxa-homepage.json'
        }
      }
    }
  }
}
