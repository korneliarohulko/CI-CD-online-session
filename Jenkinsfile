pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        script {
          checkout scm
          def customImage = docker.build("${registry}:${env.BUILD_ID}")
        }

      }
    }
    
    stage('unit-test') {
      steps {
        script {
          docker.image("${registry}:${env.BUILD_ID}").inside {c ->
          sh 'python app_test.py'}
        }
      }
    }
    
    stage('http-test') {
      steps {
        script {
          docker.image("${registry}:${env.BUILD_ID}").withRun('-p 9005:9000') {c ->
          sh "sleep 5; curl -i http://localhost:9005/test_string"}
        }

      }
    }

    stage('Publish') {
      steps {
        script {
          docker.withRegistry('', 'dockerhub_id') {
            docker.image("${registry}:${env.BUILD_ID}").push("${env.BUILD_ID}")
          }
        }

      }
    }
    
    stage('Deploy') {
      steps {
        sh 'docker stop flask-app || true; docker rm flask-app || true;docker run -d --name flask-app -p 9000:9000 vpanton/flask-app:latest'
      }
    }

  }
  environment {
    registry = 'kornelia22322/cicd'
  }
}
