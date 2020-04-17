pipeline {
    agent {
        node {
            label 'master'
        }
    }

    stages {
        stage('terraform clone') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '2fb7dff5-dcb5-4e2f-94d0-90ef7ecc49a6', url: 'https://github.com/MahendraAllada/mahigods.git']]])
            }
        }
        stage('Parameters'){
            steps {
                sh label: '', script: ''' sed -i \"s/user/$access_key/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/password/$secret_key/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/t2.micro/$instance_type/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/10/$instance_size/g\" /var/lib/jenkins/workspace/django/ec2.tf
sed -i \"s/ap-south-1/$instance_region/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/ap-south-1a/$availability_zone/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/alladamumbai/$key/g\" /var/lib/jenkins/workspace/django/variables.tf
sed -i \"s/ami-0470e33cd681b2476/$Image/g\" /var/lib/jenkins/workspace/django/variables.tf
'''
                  }
            }
            
        stage('terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('terraform plan') {
            steps {
                sh 'terraform plan'
            }
        }
        stage('terraform apply') {
            steps {
                sh 'terraform apply -auto-approve'
                sleep 150
            }
        }
         stage('Application Deployment') {
            steps {
                sh label: '', script: '''pubIP=$(<publicip)
                echo "$pubIP"
                ssh -tt -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no ec2-user@$pubIP /bin/bash << EOF
                git clone -b branchPy https://github.com/MahendraAllada/mahigods.git
                cd mahigods/
                chmod 755 manage.py
                python manage.py migrate
                nohup ./manage.py runserver 0.0.0.0:8000 &
                exit
                EOF
                '''
            }
        }
       
    }
}
