node {
    docker.image('node:16-buster-slim').inside('-p 3000:3000 --user root') {
        stage('Setup Environment') {
            sh '''
            echo "Checking if Git is installed..."
            if ! command -v git &> /dev/null; then
                echo "Git is not installed. Installing Git..."
                apt-get update && apt-get install -y git
            else
                echo "Git is already installed."
            fi
            '''
        }
        stage('Build') {
            sh 'npm install'
        }
        stage('Test') {
            sh './jenkins/scripts/test.sh'
        }
        stage('Manual Approval') {
            try {
                input message: 'Apakah Anda ingin melanjutkan ke tahap deploy?',
                      ok: 'Proceed',
                      parameters: []
            } catch (err) {
                echo "Pipeline dihentikan oleh pengguna pada tahap Manual Approval."
                currentBuild.result = 'ABORTED'
                error("Pipeline dihentikan oleh pengguna.")
            }
        }
        stage('Deploy') {
            sh './jenkins/scripts/deliver.sh'
            withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
            sh '''
            echo 'Configuring Git in Docker environment...'
            git config --global user.name "Your Name"
            git config --global user.email "your.email@example.com"
            git config --global --add safe.directory /var/jenkins_home/workspace/react-app

            # Pastikan Git sudah diinisialisasi
            
            echo "Initializing Git repository..."
            git init
            git remote set-url origin https://${GITHUB_TOKEN}@github.com/RestuAlamBagaskara/a428-cicd-labs.git
            

            # Pastikan branch target ada
            git fetch origin react-app
            git checkout react-app
            git pull origin react-app


            echo 'Adding build files to Git and pushing to repository...'
            if [ -z "$(ls -A build)" ]; then
                echo "Error: Build directory is empty. Nothing to add to Git."
                exit 1
            fi

            git add .
            git status
            git commit -m "Jenkins: Deployed build files via deliver.sh" || echo "No changes to commit"
            git push origin react-app --verbose || echo "Failed to push changes"
            '''
            }
            sleep 60
            input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
            sh './jenkins/scripts/kill.sh'
        }
    }
}
