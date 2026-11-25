pipeline {
  agent any

  environment {
    IMAGE = 'fredndabikunze01/fred-ndabikunze-juice-ship:v6.0'
    CONTAINER_NAME = 'juice-shop-jenkins'
    HOST_PORT = '3000'
  }

  stages {
    stage('Check Docker') {
      steps {
        sh 'docker info'
      }
    }

    stage('Pull image') {
      steps {
        sh 'docker pull "$IMAGE"'
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          set -eu

          if docker container inspect "$CONTAINER_NAME" >/dev/null 2>&1; then
            docker rm --force "$CONTAINER_NAME"
          fi

          docker run --detach \
            --name "$CONTAINER_NAME" \
            --restart unless-stopped \
            --publish "127.0.0.1:${HOST_PORT}:3000" \
            "$IMAGE"
        '''
      }
    }

    stage('Verify') {
      steps {
        sh '''
          set -eu
          attempt=0

          while [ "$attempt" -lt 30 ]; do
            state=$(docker inspect --format='{{.State.Running}}' "$CONTAINER_NAME")
            if [ "$state" != "true" ]; then
              docker logs "$CONTAINER_NAME"
              exit 1
            fi

            if curl --fail --silent "http://127.0.0.1:${HOST_PORT}/" >/dev/null; then
              echo "Juice Shop is responding on localhost:${HOST_PORT}"
              exit 0
            fi

            attempt=$((attempt + 1))
            sleep 2
          done

          docker logs "$CONTAINER_NAME"
          exit 1
        '''
      }
    }
  }
}
