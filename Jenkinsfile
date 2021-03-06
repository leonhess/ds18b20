pipeline {
  agent any
  triggers {
    pollSCM('H/15 * * * *')
  }
  stages {
    stage('Build Container') {
      agent {
        label "Pi_Zero"
      }
      steps {
        sh "docker build -t ds18b20 ."
      }
    }
    stage('Tag Container') {
      agent {
        label "Pi_Zero"
      }
      steps {
        sh "docker tag ds18b20 fx8350:5000/ds18b20:latest"
        sh "docker tag ds18b20 leonhess/ds18b20:latest"
      }
    }
    stage('Push to Registries') {
      parallel {
        stage('Push to local Registry') {
          agent {
            label "Pi_Zero"
          }
          steps {
            sh "docker push fx8350:5000/ds18b20:latest"
          }
        }
        stage('Push to DockerHub') {
          agent {
            label "Pi_Zero"
          }
          steps {
            withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
              sh "docker push leonhess/ds18b20:latest"
            }
          }
        }
      }
    }
    stage('Cleanup') {
      agent {
        label "Pi_Zero"
      }
      steps {
        sh "docker rmi fx8350:5000/ds18b20:latest"
        sh "docker rmi leonhess/ds18b20:latest"
      }
    }
    stage('Deploy') {
      agent {
        label "master"
      }
      steps {
        ansiblePlaybook(
          playbook: 'deploy.yml',
          credentialsId: 'd36bc821-dad8-45f5-9afc-543f7fe483ad'
          )
        }
      }
    }
  }
