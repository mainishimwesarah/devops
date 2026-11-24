
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'msarahm/sarah-cat'
        TEST_CONTAINER = "juice-shop-ci-${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code downloaded successfully!'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Juice Shop Docker image...'
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing Juice Shop application...'

                sh '''
                    docker run -d \
                      --name ${TEST_CONTAINER} \
                      -p 127.0.0.1:3006:3000 \
                      ${IMAGE_NAME}:${BUILD_NUMBER}

                    for i in $(seq 1 30); do
                        if curl -fsS http://127.0.0.1:3006/ > /dev/null; then
                            echo "SUCCESS: Juice Shop is responding!"
                            exit 0
                        fi
                        sleep 2
                    done

                    docker logs ${TEST_CONTAINER}
                    exit 1
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f ${TEST_CONTAINER} || true'
        }

        success {
            echo 'Jenkins pipeline completed successfully!'
        }

        failure {
            echo 'Jenkins pipeline failed. Check the console output.'
        }
    }
}
