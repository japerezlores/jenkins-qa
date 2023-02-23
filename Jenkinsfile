/* groovylint-disable CompileStatic, NestedBlockDepth */
pipeline {
    agent {
        node {
            label 'java-node'
        }
    }

    environment {
        registryCredential = 'docker-hub-credentials'
        registryBackend = 'japerezlores/backend-demo'
    }

    stages {
        stage('Build Project') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        /* stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube-credentials', installationName: 'sonarqube-server') {
                    sh 'mvn clean verify sonar:sonar -DskipTests'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
               groovylint-disable-next-line NestedBlockDepth */
                    /*script {
                    groovylint-disable-next-line DuplicateStringLiteral */
                      /*  def qg = waitForQualityGate(webhookSecretId: 'sonarqube-credentials')
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }*/
        stage('Push Image to Docker Hub') {
            steps {
                script {
                    dockerImage = docker.build registryBackend + ':latest'
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                jacoco()
                junit 'target/surefire-reports/*.xml'
            }
        }
        stage('Deploy to K8s') {
            steps {
                script {
                    if (fileExists('configuracion')) {
                        sh 'rm -r configuracion'
                    }
                }

                /* groovylint-disable-next-line LineLength */
                sh 'git clone https://github.com/scailancrei/kubernetes-helm-docker-config.git configuracion --branch master'
                /* groovylint-disable-next-line LineLength */
                sh 'kubectl apply -f configuracion/kubernetes-deployment/spring-boot-app/manifest.yml -n default --kubeconfig=configuracion/kubernetes-config/config'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            sh 'docker rmi -f ' + registryBackend + ':latest'
        }
    }
}
