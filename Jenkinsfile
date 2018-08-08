#!/usr/bin/env groovy

pipeline {
  agent none
  environment {
    PAGE_URL = "http://ufix.com"
  }
  options {
    ansiColor('xterm')
    timestamps()
    timeout(time: 30, unit: 'MINUTES')
  }
  stages {
    stage('clone') {
       agent any
       steps {
         checkout([
           $class: 'GitSCM',
           branches: [[name: 'master']],
           extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'openwpm']],
           userRemoteConfigs: [[url: 'https://github.com/citp/OpenWPM']]])
        }
    }
    stage('Run OpenWPM-provided demo') {
      agent {
        dockerfile { dir 'openwpm' }
      }
      steps {
        sh '''
        mkdir -p docker-volume && docker run -v $PWD/docker-volume:/home/user/ \
        -it openwpm python /opt/OpenWPM/demo.py
        '''
      }
    }
    stage('Results') {
      agent {
        dockerfile {
          args '--net host'
        }
      }
      steps {
        unstash 'openwpm-results.json'
      }
    }
  }
}
