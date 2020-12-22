pipeline {
  agent none
  stages {
    stage('Build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('Unit Test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        sh 'mvn clean test'
      }
    }

    stage('Package') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }
      }
      when {
        anyOf {
          branch 'master'
          branch 'stage'
        }
      }
      steps {
        sh 'mvn package -DskipTests'
      }
    }

    stage('Docker B & P') {
      when {
        anyOf {
          branch 'master'
          branch 'stage'
        }

      }
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
            def dockerImage = docker.build("kasera1/sysfoo:v${env.BUILD_ID}", "./")

            dockerImage.push()

            dockerImage.push("latest")
            dockerImage.push("dev")

          }
        }

      }
    }

    stage('deploy dev') {
      agent any
      steps {
        sh ' docker-compose up -d'
      }
    }
    
  }
  tools {
    maven 'Maven 3.6.3'
  }
}
