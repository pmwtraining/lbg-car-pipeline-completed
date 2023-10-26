pipeline {
    agent any
    environment {
        SERVER_URL = "http://127.0.0.1:8000/" // replace with IP of second server
        MYSQL_ROOT_PASSWORD = credentials('MYSQL_ROOT_PASSWORD')
    }
    stages {
        stage('Checkout source repos') {
            steps {
                dir("lbg-car-front") {
                    git url: "https://github.com/agray998/lbg-car-react-starter", branch: "main"
                }
                dir("lbg-car-back") {
                    git url: "https://github.com/agray998/lbg-car-spring-app-starter", branch: "main"
                }
            }
        }
        stage('Test and build spring backend') {
            steps {
                dir("lbg-car-back") {
                    sh "mvn clean test"
                    sh "docker build -t agray998/lbg-car-back:v${BUILD_NUMBER} --build-arg MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} ."
                    sh "docker tag agray998/lbg-car-back:v${BUILD_NUMBER} agray998/lbg-car-back:latest"
                }
            }
        }
        stage('Test and build react frontend') {
            steps {
                dir("lbg-car-front") {
                    sh """
                    yarn test
                    docker build -t --build-arg SERVER_URL=${SERVER_URL} agray998/lbg-car-front:v${BUILD_NUMBER} .
                    docker tag agray998/lbg-car-front:v${BUILD_NUMBER} agray998/lbg-car-front:latest
                    """
                }
            }
        }
        stage('Push docker images and cleanup') {
            steps {
                sh "docker push agray998/lbg-car-front:v${BUILD_NUMBER}"
                sh "docker push agray998/lbg-car-front:latest"
                sh "docker push agray998/lbg-car-back:v${BUILD_NUMBER}"
                sh "docker push agray998/lbg-car-back:latest"
                sh "docker rmi agray998/lbg-car-back agray998/lbg-car-back:v${BUILD_NUMBER} agray998/lbg-car-front agray998/lbg-car-front:v${BUILD_NUMBER}"
            }
        }
        stage('Deploy to server') {
            steps {
                sh "scp -i ~/.ssh/server_key docker-compose.yaml jenkins@cardb-server:/home/jenkins/app/docker-compose.yaml"
                sh """
                ssh -i ~/.ssh/server_key jenkins@cardb-server <<EOF
                export MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
                cd app
                docker-compose up -d
                """
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
