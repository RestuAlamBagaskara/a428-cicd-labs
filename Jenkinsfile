node {
    stage('Build') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            echo 'Starting Build Stage'
            sh '''
                echo "Installing dependencies..."
                npm install
            '''
        }
    }

    stage('Test') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            echo 'Running Tests'
            sh '''
                echo "Executing test scripts..."
                ./jenkins/scripts/test.sh
            '''
        }
    }
}