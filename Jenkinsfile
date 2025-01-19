node {
    stage('Build') {
        docker.image('node:16-buster-slim').inside {
            sh 'npm install'
        }
    }

    stage('Test') {
        docker.image('node:16-buster-slim').inside {
            sh './jenkins/scripts/test.sh'
        }
    }
}