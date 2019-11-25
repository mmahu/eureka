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
                sh 'docker service create \
                    --limit-memory 512M \
                    --hostname mmahu-eureka \
                    --no-resolve-image \
                    --name mmahu-eureka \
                    --publish published=8761,target=8761 \
                    mmahu-main:5000/mmahu-eureka:1.0.$BUILD_NUMBER'
            }
        }
    }
}