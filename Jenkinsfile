pipeline{
    agent any
    parameters{
        string(name: 'JENKINS_ADMIN_PUBLIC_IP', defaultValue: '', description: 'My IP address Public IP')
        string(name: 'JENKINS_ADMIN_LOCATION', defaultValue: '', description: 'Location of the admin user')
        string(name: 'DATE_TIME', defaultValue: '', description: 'Date and time of the change')
    }
    environment{
        AWS_REGION = 'eu-north-1'
        JENKINS_SECURITY_GROUP_ID = 'sg-07712f4d00378fd1c'
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 3, unit: 'MINUTES')
    }
    stages{
        stage('Change IP'){
            steps{
                echo 'Changing IP in security group'
                withCredentials([usernamePassword(credentialsId: 'aws_cli_access_store', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh(
                        script:
                            """
                                aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                                aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                                aws configure set default.region ${AWS_REGION}
                                aws ec2 authorize-security-group-ingress --group-id ${JENKINS_SECURITY_GROUP_ID} --protocol tcp --port 22 --cidr ${params.JENKINS_ADMIN_PUBLIC_IP}/32 --description "SSH admin access from ${params.JENKINS_ADMIN_LOCATION} at ${params.DATE_TIME}" --output text
                                aws ec2 authorize-security-group-ingress --group-id ${JENKINS_SECURITY_GROUP_ID} --protocol tcp --port 8080 --cidr ${params.JENKINS_ADMIN_PUBLIC_IP}/32 --description "Jenkins admin access from ${params.JENKINS_ADMIN_LOCATION} at ${params.DATE_TIME}" --output text
                                aws ec2 authorize-security-group-ingress --group-id ${JENKINS_SECURITY_GROUP_ID} --protocol tcp --port 8082 --cidr ${params.JENKINS_ADMIN_PUBLIC_IP}/32 --description "Artifactory admin access from ${params.JENKINS_ADMIN_LOCATION} at ${params.DATE_TIME}" --output text
                            """
                    )
                }        
            }
        }
    }
}