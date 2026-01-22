pipeline {
  agent any

  environment {
    DEV_IP   = "43.204.103.71"
    STAGE_IP = "13.126.172.229"
    PROD_IP  = "13.201.78.57"
  }

  stages {
    stage('Deploy') {
      steps {
        script {
          if (env.BRANCH_NAME == 'dev') {
            deploy(DEV_IP, "DEV")
          }
          else if (env.BRANCH_NAME == 'stage') {
            deploy(STAGE_IP, "STAGE")
          }
          else if (env.BRANCH_NAME == 'main') {
            deploy(PROD_IP, "PROD")
          }
        }
      }
    }
  }
}

def deploy(ip, envName) {
  sh """
    echo "Deploying to ${envName} server"
     sudo yum install git -y
    # copy files to EC2
    scp -o StrictHostKeyChecking=no -r ./* ec2-user@${ip}:/tmp/app

    # deploy on EC2 (single ssh command)
    ssh -o StrictHostKeyChecking=no ec2-user@${ip} "
      sudo yum install -y httpd || true &&
      sudo systemctl enable httpd &&
      sudo systemctl start httpd &&
      sudo rm -rf /var/www/html/* &&
      sudo cp -r /tmp/app/* /var/www/html/ &&
      sudo systemctl restart httpd
    "
  """
}
