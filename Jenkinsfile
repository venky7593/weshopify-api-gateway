pipeline{
    agent{
        label "worker-01-jenkins"
    }
    tools{
        maven 'MAVEN_HOME'
    }
    stages{
        stage("Pull the Code From SCM"){
            steps{
                echo "========Pull the Code From SCM========"
                git branch: 'master',
                    url: 'https://github.com/Narsi-Myteaching/weshopify-platform-api-gateway.git'
                echo "========source code pulling completed========"
            }
        }
        stage("Build the Source Code"){
            steps{
                echo "========Code Building is Starting========"
                sh 'mvn clean package -DskipTests=true'
                echo "========Artifact Generated========"
            }
        }
        stage("Sonar Quality Analysis"){
            steps{
                echo "========Sonar Quality Gate Starting========"
                sh 'mvn verify sonar:sonar -Dsonar.projectKey=weshopify-platform-api-gateway -Dsonar.host.url=http://13.127.8.30:9000 -Dsonar.login=sqp_0ae649dd583abf6aca85d0c7cfb753272516790a -DskipTests=true'
                echo "========Sonar Quality Gate Analyzed the Artifact========"
            }
        }
        stage("Deploy to Artifactory"){
            steps{
                echo "========Deploying to Artifactory Started========"
                sh 'mvn deploy'
                echo "========Artifact Deploy is Completed========"
            }
        }
        stage("copy the files to ansible server"){
            steps{
                echo "Connecting to Ansible Server"
                sshagent(['ANSIBLE_SERVER']){
                    sh 'scp Dockerfile ansible-admin@172.31.7.122:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp weshopify-api-gateway-playbook.yml ansible-admin@172.31.7.122:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp jfrog.sh ansible-admin@172.31.7.122:/opt/weshopify-api-gateway/ci-files'
                    sh '''
                        ssh -tt ansible-admin@172.31.7.122 << EOF
                            ansible-playbook /opt/weshopify-api-gateway/ci-files/weshopify-api-gateway-playbook.yml
                            exit
                        EOF
                    '''
                }
            }
        }
    }
}
