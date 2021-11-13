pipeline{
        agent {
            label 'master'
        }
        options{
            buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
        }
        stages{
            stage("Packaging"){
                steps {
                    sh 'mvn package -Pprod -Dmaven.test.skip=true'
                }
            }
            stage("Delivering artifact to AWS"){
                steps {
                    echo 'Delivering artifact to remote server...'
                    dir ('target') {
                        sh 'scp feedby-0.0.1-SNAPSHOT.jar jenkins@enumerable-entity.link:/home/jenkins/app'
                    }
                }
            }
            stage("Deploy artifact"){
                 steps {
                     script{
                     echo 'Deploying...'
                     sshagent(credentials:['jenkinsAWSssh']){
                       sh """ ssh jenkins@enumerable-entity.link << EOF
                       docker restart SpringAppRunner
                       exit
                       EOF """
                     }
                     }
                    }
                }
            }
        }

