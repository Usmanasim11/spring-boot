pipeline {
    agent any
    
    stages {
//         stage('Checkout') {
//             steps {
//                 checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mhassanmanzoorsl/spring-boot-jacoco.git']])
//             }
//         }

        stage('Build Jar') {
            steps {
             //   sh 'mvn clean install'
                sh 'mvn clean package'
               // sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                //sh 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }
        
        stage('Publishing Junit Tests report ') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }   
        }
        stage('Publishing Code Covergae') {
            steps {
                jacoco()
            }   
        }
//         stage('SonarQube Analysis') {
//             steps {
//                 withSonarQubeEnv('sonarqube') {
//                     sh '''
//                         mvn clean verify sonar:sonar -X\
//                                 -Dsonar.projectKey=sonarqube-demo-f \
//                                 -Dsonar.projectName='sonarqube-demo-f' \
//                                 -Dsonar.host.url=http://20.54.72.51:9000 \
//                                 -Dsonar.token=sqp_115fd2a761d4397b7a8f186a9eae9c4f21378f31
//                     '''
//                 }
//             }
//         }
        
        
//          stage('SonarQube Analysis') {
//              steps {
//                  withSonarQubeEnv('sonarqube') {
//                      sh 'sonar-scanner \
//                          -Dsonar.projectKey=sonar-spring \
//                          -Dsonar.projectName=\'sonar-spring\' \
//                          -Dsonar.host.url=http://20.54.72.51:9000 \
//                          -Dsonar.token=sqp_773187028187880bf2a44475ffa17d9a67482a62'
//                  }
//              }
//          }
        
//         stage('Image push to Docker Hub') {
//             steps {
//                 sh 'docker version'
//                 sh 'docker build -t mhassanmanzoorsl/springbootjacoco:0.0.1 -f Dockerfile .'
//                 withDockerRegistry(credentialsId: 'Docker-Hub-Registry-mhassanmanzoorsl-creds', url: 'https://index.docker.io/v1/') {
//                 sh 'docker push mhassanmanzoorsl/springbootjacoco:0.0.1'
//                 }
//             }
//         }

        stage('Image Build') {
            steps {
                sh 'docker version'
                sh 'docker build -t 192.168.1.80:5000/springbootjacoco:0.0.1 -f Dockerfile .'
            }
        }
        
        stage('Image Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker_local_registry', url: 'https://localregistry.com:5000/v2/') {
                sh 'docker push localregistry.com:5000/springbootjacoco:0.0.1'
                }
            }
        }
 

//          stage ('Image push to Nexus Local') {
//              steps {
//                  script {
//                      docker.withRegistry('http://192.168.1.80:9000/repository/nexus-docker-local/', 'superuser-for-nexus')
//                      //withDockerRegistry(credentialsId: 'superuser-for-nexus', url: 'http://192.168.1.80:9000/repository/nexus-docker-local/') 
//                      {
//                          def customImage = docker.build("springbootjacoco:0.0.1", "-f Dockerfile .")
//                          customImage.push() 
//                      }
//                  }
//              }
//          }
        
        

        stage('Deploy to K8 Cluster') {
            steps {
                sshagent(['k8s_master_ssh_key']) {
                    //sh 'scp -r -o StrictHostKeyChecking=no springbootappk8s.yaml root@192.168.1.60:/'
                    //sh 'scp -r -o StrictHostKeyChecking=no springk8snexus.yaml root@192.168.1.60:/'
                    sh 'scp -r -o StrictHostKeyChecking=no springk8slocalregistry.yaml root@192.168.1.60:/'
                    script{
                    try{
                        //sh 'ssh root@192.168.1.60 kubectl apply -f /springbootappk8s.yaml --kubeconfig=/root/.kube/config'
                        //sh 'ssh root@192.168.1.60 kubectl apply -f /springk8snexus.yaml --kubeconfig=/root/.kube/config'
                        sh 'ssh root@192.168.1.60 kubectl apply -f /springk8slocalregistry.yaml --kubeconfig=/root/.kube/config'
                        }
                    catch(error)
                       {}
                    }
                }
            }
        }
    }
}
