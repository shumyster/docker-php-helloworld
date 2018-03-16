node {
    
    // wipe workspace
    deleteDir()
    
    stage('Checkout Code') {
    git 'https://github.com/mattrussell33/docker-php-helloworld.git'
    }

    stage('Docker Build') {
    docker.build('docker-php-helloworld')
    }

    stage ('Docker Push') {
        docker.withRegistry('https://381145069197.dkr.ecr.eu-west-1.amazonaws.com', 'ecr:eu-west-1:russellm-ecr-credentials') {
          docker.image('docker-php-helloworld').push('latest')
        }
    }

    stage('Build new image from ECR to ECS') {
    // Use list-tasks and sed to store task ID as variable. 
    // Task ID does not equal task-definition, hence the need to retrieve this from list command
    sh '''TASK_ID=`aws ecs list-tasks --cluster php-helloworld --desired-status RUNNING --family docker-php-helloworld | egrep "task" | tr "/" " " | tr "[" " " |  awk \'{print $2}\' | sed \'s/"$//\'
`
aws ecs stop-task --cluster php-helloworld --task ${TASK_ID}
aws ecs update-service --cluster php-helloworld --service php-helloworld-service --task-definition docker-php-helloworld:3 --desired-count 1'''
    }
}
