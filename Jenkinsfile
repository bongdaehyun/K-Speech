pipeline {
  agent any

  tools {
    gradle "gradle"
  }

  options { skipDefaultCheckout(false) }

  stages {
    stage('Preparation') {
      steps {
        script {
          env.ymd = sh (returnStdout: true, script: ''' echo date '+%Y%m%d-%H%M%S' ''')
        }
        echo("params : ${env.ymd} " + params.tag)
      }
    }

    stage('Build') {
      steps {
        sh "cd ./spring-cloud/backend && chmod +x gradlew && ./gradlew clean build"
        sh "cd ./spring-cloud/eureka && chmod +x gradlew && ./gradlew clean build"
        sh "cd ./spring-cloud/game && chmod +x gradlew && ./gradlew clean build"
        sh "cd ./spring-cloud/gateway && chmod +x gradlew && ./gradlew clean build"
      }
    }

    stage('Docker Del') {
      steps {
        sh "docker rm -f eureka && docker rmi eureka"
        sh "docker rm -f gateway && docker rmi gateway"
        sh "docker rm -f back && docker rmi back"
        sh "docker rm -f game && docker rmi game"
        sh "docker rm -f front && docker rmi front"

      }
    }

    stage('Docker Build') {
      steps {
        sh "docker build -t eureka ./spring-cloud/eureka"
        sh "docker build --build-arg ENVIRONMENT=prod -t gateway ./spring-cloud/gateway"
        sh "docker build -t back ./spring-cloud/backend"
        sh "docker build -t game ./spring-cloud/game"
        sh 'docker build -t front ./frontend'
      }
    }

    stage('Docker Deploy') {
      steps {
        sh "docker run -d --name eureka -p 8761:8761 eureka"
        sh "docker run -d --name gateway -p 8000:8000 gateway"
        sh "docker run -d --name back --network kspeech -p 8080:8080 back"
        sh "docker run -d --name game --network kspeech -p 8081:8081 game"
        sh "docker run -d --name front -p 3000:80 -p 443:443 -v /home/ubuntu/sslkey:/usr/share/nginx/html/homepage/cert  --network kspeech front"
 

      }
    }

  }


}