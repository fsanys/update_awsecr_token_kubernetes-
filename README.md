# update_ecr_token_kubernetes

## Presentation

If you are using AWS ECR repository in a kubernetes cluster outside AWS, you will need to update your secret used to connect to AWS ECR few times a day.
In fact the token generated by "docker login" will expire every twelve hours. The secret key created initially won't be valid after 12 hours and you won't be able to connect to AWS ECR registry.

To automate the change of the token you can use this Jenkins Pipeline launched as a cron every 6 hours.
The Jenkins Pipeline will connect to AWS ECR using a Docker container then use a Kubernetes container to update the secret in Kubernetes.

The role of the pipeline is to change the secret defined in the namespace of the app which use a repository at AWS ECR. AWS credentials are defined via CloudBees plugin: CloudBees Amazon Web Services Credentials Plugin
FYI, if kubernetes is hosted in AWS, you just need to enable the permission via IAM.

The pipeline consists of 4 parts:
 * Initialisation of the variables and the trigger (every 6 hours)
 * Create 2 PodTemplate, aws and kubectl and share the docker socket (Using Docker Hub)
 * AWS, connect using the AWS credentials stored in Jenkins and connect using docker client and get the password
 * KUBCETL, update the secret using the password obtained via docker client




## How to use

1. Install and Initialize AWS credentials plugin

In Jenkins, go to "Manage Plugins", then search for the available package and install "CloudBees Amazon Web Services Credentials Plugin"

Once installed, create the credential using your ACCESS KEY ID/PASSWORD.

2. Create the pipeline

Copy and save the Pipeline below and change the attributes at the beginning of the file:
 * JENKINS_AWS_CREDENTIALS_NAME: the name of the credential created for AWS connection
 * K8s_namespace: Kubernetes namespace where you need to update the secret
 * K8s_keyname: Kubernetes secret used
 * K8s_docker_email: Docker email
 * ECR_repository_url: URL of the AWS ECR repository
