pipeline {
    agent any
    stages {
        stage('fetch') {
            steps {
                git url: 'https://github.com/mmahu/eureka.git', branch: 'master'
            }
        }
        stage('build') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew clean assemble -PbuildNumber=1.0.$BUILD_NUMBER'
            }
        }
        stage('imaging') {
            steps {
                sh 'docker build . -t mmahu-main:5000/mmahu-eureka:1.0.$BUILD_NUMBER'
                sh 'docker push mmahu-main:5000/mmahu-eureka'
            }
        }
        stage('deploy') {
            steps {
                sh 'docker service rm mmahu-eureka || true'
                sh 'docker service create --limit-memory 512M --network dev --hostname mmahu-eureka --no-resolve-image --replicas 1 --name mmahu-eureka mmahu-main:5000/mmahu-eureka:1.0.$BUILD_NUMBER'
            }
        }
    }
}