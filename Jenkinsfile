def name=""
def port=""
def registry=""
def buildNumber=""

pipeline {
    agent any
    stages {
        stage('init') {
            steps {
                script {
                    name = "e-eureka"
                    port = "8761:8761"
                    registry = "192.168.0.19:5000"
                    buildNumber = "1.0.$BUILD_NUMBER"
                }
            }
        }
        stage('fetch') {
            steps {
                git url: 'https://github.com/mmahu/eureka.git', branch: 'master'
            }
        }
        stage('build') {
            steps {
                sh 'chmod +x gradlew'
                sh "echo ${buildNumber}"
                sh "./gradlew clean assemble -PbuildNumber=${buildNumber}"
            }
        }
        stage('imaging') {
            steps {
                sh "docker build . -t ${registry}/${name}:${buildNumber}"
                sh "docker push ${registry}/${name}"
            }
        }
        stage('deploy') {
            steps {
                sh "docker service rm ${name} || true"
                sh "docker service create \
                    --name ${name} \
                    --no-resolve-image \
                    --publish ${port} \
                    ${registry}/${name}:${buildNumber}"
            }
        }
    }
}