pipeline {
    agent any
    stages {
        stage('Stage1-GitClone') {
            steps {
                // Get code from a GitHub repository
                // git 'https://github.com/KarenArzumanyan/devops-pw11-CI.git'
                // Show getting file list
                sh 'ls -l'
            }
        }
        stage('Stage2-DockerRun') {
            steps {
                sh 'docker build . -t devops/pw11_nginx'
                sh 'docker run --name devops-pw11 -d -p 9889:80 devops/pw11_nginx'
            }
        }
        stage('Stage3-Test1-Satus200') {
            steps {
                script {
                    sh '''
                        status_code=$(curl --write-out %{http_code} --silent --output /dev/null http://localhost:9889/index.html)
                        echo "Status code: $status_code"

                        if [[ $status_code -ge 200 ]]
                        then
                          curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='response code 200'
                        else
                          curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='response code not 200'
                        fi
                    '''
                }
            }
        }    
        stage('Stage4-Test2-MD5SUM') {
            steps {
                script {
                    sh '''
                        online_md5="$(curl -sL http://localhost:9889/index.html | md5sum | cut -d ' ' -f 1)"
                        local_md5="$(md5sum "index.html" | cut -d ' ' -f 1)"

                        echo "Online MD5: $online_md5"
                        echo "Local MD5: $local_md5"

                        if [[ $online_md5 = $local_md5 ]]
                        then
                            echo "hurray, they are equal!"
                        else
                         exit 0
                        fi

                        if [[ $status_code -ge 200 ]]
                        then
                          curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='MD5 OK'
                        else
                          curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='MD5 not OK'
                        fi
                    '''
                }
            }
        }
        stage('Stage5-Docker-Cleare') {
            steps {
                sh '''
                    docker container stop devops-pw11
                    docker container rm devops-pw11
                    docker image rm devops/pw11_nginx
                '''
            }
        }
}
    post {
        success {
                sh  '''
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : OK *Published* = YES'
                '''
        }

        aborted {
                sh '''
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : `Aborted` *Published* = `Aborted`'
                '''
        }
        failure {
            steps {
                sh '''
                    docker container stop devops-pw11
                    docker container rm devops-pw11
                    docker image rm devops/pw11_nginx
                '''
                sh  '''
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC  *Branch*: ${env.GIT_BRANCH} *Build* : `not OK` *Published* = `no`'
                '''
            }
        }
    }
}