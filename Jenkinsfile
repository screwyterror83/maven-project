pipeline {
    agent any
    
    tools {
        maven 'localMaven'
    }
    parameters {
        string(name: 'tomcat_dev', defaultValue: '54.183.230.165', description: 'Tomcat_dev server on aws')
        string(name: 'tomcat_prod', defaultValue: '13.57.182.60', description: 'Tomcat_prod server on aws')
    }
    triggers {
        pollSCM('* * * * *')
    }
stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /Users/dazhao/Sandbox/tomcat-aws-demo/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /Users/dazhao/Sandbox/tomcat-aws-demo/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}