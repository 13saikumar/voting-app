pipeline {
  agent none
  stages {
    stage('Worker Build') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        echo 'Building a project'
        dir(path: 'worker') {
          sh 'mvn compile'
        }

      }
    }

    stage('Worker Test') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        echo 'Testing the project'
        dir(path: 'worker') {
          sh 'mvn test'
        }

      }
    }

    stage('Worker docker-Package') {
      agent any
      when {
        changeset '**/worker/**'
      }
      steps {
        echo 'Packaging the product using docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImg = docker.build("itachi0139/workerbuild:v${BUILD_ID}","./worker")
            workerImg.push()
            workerImg.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('Vote Build') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root -v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        echo 'Building a project'
        dir(path: 'vote') {
          sh 'pip install -r requirements.txt'
        }

      }
    }

    stage('Vote Test') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root -v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        echo 'Testing the project'
        dir(path: 'vote') {
          sh 'pip install -r requirements.txt'
          sh 'nosetests -v'
        }

      }
    }

    stage('Vote docker-Package') {
      agent any
      when {
        changeset '**/vote/**'
      }
      steps {
        echo 'Packaging the product using docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImg = docker.build("itachi0139/votebuild:v${BUILD_ID}","./vote")
            workerImg.push()
            workerImg.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('Result Build') {
      agent {
        docker {
          image 'node:8.16.0-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      steps {
        echo 'Building a project'
        dir(path: 'result') {
          sh 'npm install'
        }

      }
    }

    stage('Result Test') {
      agent {
        docker {
          image 'node:8.16.0-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      steps {
        echo 'Testing the project'
        dir(path: 'result') {
          sh 'npm install'
          sh 'npm test'
        }

      }
    }

    stage('Result docker-Package') {
      agent any
      when {
        changeset '**/result/**'
      }
      steps {
        echo 'Packaging the product using docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImg = docker.build("itachi0139/resultbuild:v${BUILD_ID}","./result")
            workerImg.push()
            workerImg.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('Deploy to Dev') {
      agent any
      steps {
        sh 'docker compose up -d'
      }
    }

  }
  post {
    always {
      echo 'Pipeline execution is complete....'
    }

  }
}