#!groovy
pipeline
{
    options
    {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }
    agent any
	// Define Environemnt Variable 
    environment 
    {
	VERSION = '${env.BUILD_NUMBER}'
        PROJECT = 'aws-jenkins-integration'
        IMAGE = 'aws-jenkins-integration:VERSION'
        ECRURL = 'https://920995523917.dkr.ecr.us-east-1.amazonaws.com'
        CRED = 'ecr:us-east-1:demo_aws_cred'
    }
    stages
    {
	// Clone git repository
	stage ('Clone repository') 
	{
	steps 
	{
	git poll: true,url: 'https://github.com/subhamproject/docker-blog-post.git'
	}
	}
	// Build Docker image
     stage('Build Docker Image')
        {
            steps
            {
                script
                {
                    // Build the docker image using a Dockerfile
                    docker.build('${IMAGE}')
                }
            }
        }
		// Push Docker images to AWS ECR Or Docker HUB as applicable
        stage('Push Image to ECR')
        {
            steps
            {
                script
                {
                  docker.withRegistry(ECRURL,CRED)
                //  docker.withRegistry('https://920995523917.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:demo_aws_cred')
				  
                    {
			    docker.image(${IMAGE}).push()
                    }
                }
            }
        }
    }

    post
    {
        always
        {
            // make sure that the Docker image is removed
            sh "docker rmi ${IMAGE} | true"
        }
		success {
      // notify users when the Pipeline fails
      mail to: 'smandal@rythmos.com',
          subject: "Sucess: ${currentBuild.fullDisplayName}",
          body: "successfully build ${env.BUILD_URL}"
    }
	failure {
      // notify users when the Pipeline fails
      mail to: 'smandal@rythmos',
          subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
          body: "Something is wrong with ${env.BUILD_URL} Please Check the logs"
    }
	unstable {
      // notify users when the Pipeline fails
      mail to: 'smandal@rythmos',
          subject: "Unstable Pipeline: ${currentBuild.fullDisplayName}",
          body: "Build is not stable,Please check the logs ${env.BUILD_URL}"
    }
    }
}
