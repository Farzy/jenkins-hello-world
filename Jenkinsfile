/* Requires the Docker Pipeline plugin */
pipeline {
    agent { docker { image 'node:16.17.1' } }

    triggers {
        issueCommentTrigger('.*REBUILD.*')
        cron('*/3 * * * *')
    }

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
        VERBOSE      = 'false'
        MYTAG        = sh(returnStdout: true, script: 'git describe --tags --exact-match || true').trim()
    }

    stages {
        stage('Build trigger') {
            steps {
                script {
                    def triggerCause = currentBuild.rawBuild.getCause(org.jenkinsci.plugins.pipeline.github.trigger.IssueCommentCause)

                    if (triggerCause) {
                        echo("Build was started by ${triggerCause.userLogin}, who wrote: " +
                             "\"${triggerCause.comment}\", which matches the " +
                             "\"${triggerCause.triggerPattern}\" trigger pattern.")
                    } else {
                        echo('Build was not started by a trigger')
                    }
                }
            }
        }
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
        stage('env vars') {
            options {
                timestamps()
            }
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
                sh 'printenv'
            }
        }
        stage('Verbose') {
            when {
                environment name: 'VERBOSE', value: 'true'
            }
            steps {
                echo 'Run PS'
                sh 'ps aux'
            }
        }
        stage('random') {
            steps {
                sh "dd if=/dev/urandom bs=64 count=1 | sha256sum > randomfile.txt"
                sh "dd if=/dev/urandom bs=64 count=10 | sha256sum > randomfile2.txt"
            }
        }
        stage('Draft PR') {
            when {
                expression {
                    env.CHANGE_ID && pullRequest.draft
                }
            }
            steps {
                println "We are in PR#${env.CHANGE_ID}"
                println "This is a draft"
            }
        }
        stage('Not a Draft PR') {
            when {
                expression {
                    env.CHANGE_ID && !pullRequest.draft
                }
            }
            steps {
                println "We are in PR#${env.CHANGE_ID}"
                println "This is NOT a draft"
            }
        }
        stage('tagged') {
            when {
                anyOf {
                    buildingTag()
                    // not { environment name: 'MYTAG', value: '' }
                }
            }
            steps {
                // TAG_NAME is not available when there is not tag build
                echo "BUILDING A TAG: '${TAG_NAME}'!"
                // echo "BUILDING A TAG: '${MYTAG}'!"
                echo "This stage only executes on tag build.  "
            }
        }
    }
    
    post {
        always {
            echo 'POST: This will always run'
            archiveArtifacts artifacts: 'randomfile*.txt', fingerprint: true
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
