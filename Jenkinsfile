pipeline {
    agent any

    parameters {
         string(name: 'tomcat_staging', defaultValue: '18.220.42.160', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.217.138.114', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh '/usr/local/bin/mvn clean package'
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
                        sh "scp -i /usr/local/opt/jenkins/staging.pem **/target/*.war ec2-user@${params.tomcat_staging}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /usr/local/opt/jenkins/production.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}