// TODO: Fill in pipeline stages
pipeline {
    agent any
    stages {
        stage('Checkout source repos') {
            steps {
                dir("lbg-car-front") {
                    git url: "https://github.com/agray998/lbg-car-react-starter"
                }
                dir("lbg-car-back") {
                    git url: "https://github.com/agray998/lbg-car-spring-app-starter"
                }
            }
        }
        stage('Test and build spring backend') {
            steps {
                dir("lbg-car-back") {
                    sh "mvn clean test"
                    sh "docker build -t agray998/lbg-car-back:v${BUILD_NUMBER} ."
                    sh "docker tag agray998/lbg-car-back:v${BUILD_NUMBER} agray998/lbg-car-back:latest"
                }
            }
        }
        stage('Test and build react frontend') {
            steps {
                dir("lbg-car-front") {
                    sh "yarn test"
                    sh "docker build -t agray998/lbg-car-front:v${BUILD_NUMBER} ."
                    sh "docker tag agray998/lbg-car-front:v${BUILD_NUMBER} agray998/lbg-car-front:latest"
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
                sh "echo 'TODO'"
            }
        }
    }
}