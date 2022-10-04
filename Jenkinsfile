/* Requires the Docker Pipeline plugin */
pipeline {
    agent { docker { image 'node:16.17.1-alpine' } }
    stages {
        stage('build') {
            steps {
                sh 'node --version'
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
    }
    post {
        always {
            echo 'POST: This will always run'
        }
        success {
            echo 'POST: This will run only if successful'
        }
        failure {
            echo 'POST: This will run only if failed'
        }
        unstable {
            echo 'POST: This will run only if the run was marked as unstable'
        }
        changed {
            echo 'POST: This will run only if the state of the Pipeline has changed'
            echo 'POST: For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
