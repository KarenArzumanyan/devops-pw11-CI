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
                    def response = httpRequest responseHandle: 'NONE', url: 'http://localhost:9889/index.html', wrapAsMultipart: false
                    if (response.status == 200) {
                        withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                            sh  ("""
                                     curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='response code 200'
                            """)
                        }
                        return
             } else if (response.status != 200) {
                        withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                            sh  ("""
                                     curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='response code not 200'
                            """)
                        }
                        return
                    }
                }
            }
        }
        stage('Stage4-Test2-MD5SUM') {
            steps {
                script {
                    def md5 = sh (
                         script: 'curl -s http://localhost:9889/index.html | md5sum | awk \'{print $1 " index.html"}\' | md5sum -c | awk \'{print $2}\'',
                         returnStdout: true
                    ).trim()
                    println ('Status: ' + md5)
                    if (md5 == 'OK') {
                        withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                            sh  ("""
                                     curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='MD5 OK'
                            """)
                        }
                        return
                     } else {
                        withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                            sh  ("""
                                     curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='MD5 not OK'
                            """)
                        }
                        return
                    }
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
            withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : OK *Published* = YES'
            """)
            }
        }

        aborted {
            withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : `Aborted` *Published* = `Aborted`'
            """)
            }
        }
        failure {
            withCredentials([string(credentialsId: 'chatWebid', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
                sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC  *Branch*: ${env.GIT_BRANCH} *Build* : `not OK` *Published* = `no`'
            """)
            }
        }
    }
}
