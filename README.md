# Amazon EKS
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Contents**

  - [EKS CLUSTER CREATION WORKFLOW](#eks-cluster-creation-workflow)
- [Getting Started](#getting-started)
  - [Create an AWS Account](#create-an-aws-account)
  - [Create a Workspace](#create-a-workspace)
  - [Install Kubernetes Tools](#install-kubernetes-tools)
  - [Create an IAM role for your Workspace](#create-an-iam-role-for-your-workspace)
  - [Attach the IAM role to your Workspace](#attach-the-iam-role-to-your-workspace)
  - [Update IAM Settings for your Workspace](#update-iam-settings-for-your-workspace)
  - [Clone the service repos](#clone-the-service-repos)
  - [Create an AWS KMS Customer Managed Key](#create-an-aws-kms-customer-managed-key)
- [Launch Using EKSCTL](#launch-using-eksctl)
  - [Prerequisites](#prerequisites)
  - [Launch EKS](#launch-eks)
    - [Create an EKS Cluster](#create-an-eks-cluster)
  - [Test the Cluster](#test-the-cluster)
  - [Console Credentials](#console-credentials)
    - [Import your EKS Console credentials to your new cluster:](#import-your-eks-console-credentials-to-your-new-cluster)
- [Deploy the Kubernetes Dashboard](#deploy-the-kubernetes-dashboard)
  - [Deploy the dasboard](#deploy-the-dasboard)
  - [Access the Dashboard](#access-the-dashboard)
  - [Cleanup](#cleanup)
- [Deploy the example Microservices](#deploy-the-example-microservices)
  - [Deploy our sample applications](#deploy-our-sample-applications)
  - [Deploy NodeJS Backend api](#deploy-nodejs-backend-api)
  - [Deploy Crystal Backend API](#deploy-crystal-backend-api)
  - [Let's check Service Types](#lets-check-service-types)
  - [Ensure the ELB Service Role Exists](#ensure-the-elb-service-role-exists)
  - [Deploy Frontend Service](#deploy-frontend-service)
  - [Find the service address](#find-the-service-address)
  - [Scale the Backend Services](#scale-the-backend-services)
  - [Scale the Frontend](#scale-the-frontend)
  - [Cleanup the applications](#cleanup-the-applications)
- [Helm](#helm)
  - [Introduction](#introduction)
  - [Install Helm CLI](#install-helm-cli)
  - [Deploy nginx with Helm](#deploy-nginx-with-helm)
    - [Update the Chart repository](#update-the-chart-repository)
    - [Search Chart repositories](#search-chart-repositories)
    - [Add the Bitnami Repository](#add-the-bitnami-repository)
    - [Install bitname/nginx](#install-bitnamenginx)
    - [cleanup](#cleanup)
  - [Deploy Example Microservices using Helm](#deploy-example-microservices-using-helm)
    - [Create a Chart](#create-a-chart)
    - [Customize Defaults](#customize-defaults)
      - [Replace hard-coded values with template directives](#replace-hard-coded-values-with-template-directives)
      - [Create a values.yaml file with our template defaults](#create-a-valuesyaml-file-with-our-template-defaults)
    - [Deploy the EKSDemo Chart](#deploy-the-eksdemo-chart)
      - [Use the dry-run flag to test our templates](#use-the-dry-run-flag-to-test-our-templates)
      - [Deploy the chart](#deploy-the-chart)
    - [Testing the service](#testing-the-service)
    - [Rolling Back](#rolling-back)
      - [Update the demo application chart with a breaking change](#update-the-demo-application-chart-with-a-breaking-change)
      - [Deploy the updated demo application chart:](#deploy-the-updated-demo-application-chart)
      - [Rollback the failed upgrade](#rollback-the-failed-upgrade)
    - [cleanup](#cleanup-1)
- [Health Checks](#health-checks)
  - [Configure Liveness Probe](#configure-liveness-probe)
    - [Configure the Probe](#configure-the-probe)
    - [Introduce a Failure](#introduce-a-failure)
    - [Challenge:](#challenge)
  - [Configure Readiness probe](#configure-readiness-probe)
    - [Configure the Probe](#configure-the-probe-1)
    - [Introduce a Failure](#introduce-a-failure-1)
    - [Challenge:](#challenge-1)
  - [cleanup](#cleanup-2)
- [Autoscaling our Applications and Cluster](#autoscaling-our-applications-and-cluster)
  - [Install kube-ops-view](#install-kube-ops-view)
  - [Configure Horizontal Pod Autoscaler (HPA)](#configure-horizontal-pod-autoscaler-hpa)
    - [Deploy the Metrics Server](#deploy-the-metrics-server)
  - [Scale an application with HPA](#scale-an-application-with-hpa)
    - [Deploy a Sample App](#deploy-a-sample-app)
    - [Create an HPA resource](#create-an-hpa-resource)
    - [Generate load to trigger scaling](#generate-load-to-trigger-scaling)
  - [Configure Cluster Autoscaler (CA)](#configure-cluster-autoscaler-ca)
    - [Configure the ASG](#configure-the-asg)
    - [IAM roles for service accounts](#iam-roles-for-service-accounts)
    - [Deploy the Cluster Autoscaler (CA)](#deploy-the-cluster-autoscaler-ca)
  - [Scale a cluster with CA](#scale-a-cluster-with-ca)
    - [Deploy a Sample App](#deploy-a-sample-app-1)
    - [Scale our ReplicaSet](#scale-our-replicaset)
  - [cleanup](#cleanup-3)
- [Introduction to RBAC](#introduction-to-rbac)
  - [What is RBAC?](#what-is-rbac)
    - [Objectives for this module](#objectives-for-this-module)
  - [Install the test Pods](#install-the-test-pods)
  - [Create a user](#create-a-user)
  - [Map an IAM User to K8s](#map-an-iam-user-to-k8s)
  - [Test the new user](#test-the-new-user)
  - [Create the role and the role binding](#create-the-role-and-the-role-binding)
  - [Verify the role and binding](#verify-the-role-and-binding)
  - [cleanup](#cleanup-4)
- [Using IAM groups to manage Kubernetes cluster access](#using-iam-groups-to-manage-kubernetes-cluster-access)
  - [Kubernetes authenticationIn the intro to RBAC module, we have seen how we can give access to individual users to Kubernetes.](#kubernetes-authenticationin-the-intro-to-rbac-module-we-have-seen-how-we-can-give-access-to-individual-users-to-kubernetes)
  - [Create IAM Roles](#create-iam-roles)
  - [Create IAM Groups](#create-iam-groups)
    - [Create k8sAdmin IAM Group](#create-k8sadmin-iam-group)
    - [Create k8sDev IAM Group](#create-k8sdev-iam-group)
    - [Create k8sInteg IAM Group](#create-k8sinteg-iam-group)
  - [Create IAM Users](#create-iam-users)
  - [Configure Kubernetes RBAC](#configure-kubernetes-rbac)
    - [Create kubernetes namespaces](#create-kubernetes-namespaces)
    - [Configuring access to development namespace](#configuring-access-to-development-namespace)
    - [Configuring access to integration namespace](#configuring-access-to-integration-namespace)
  - [Configure Kubernetes Role Access](#configure-kubernetes-role-access)
    - [Gives Access to our IAM Roles to EKS Cluster](#gives-access-to-our-iam-roles-to-eks-cluster)
  - [Test EKS Access](#test-eks-access)
    - [Automate assumerole with aws cli](#automate-assumerole-with-aws-cli)
    - [Using AWS profiles with the Kubectl config file](#using-aws-profiles-with-the-kubectl-config-file)
      - [With dev profile](#with-dev-profile)
      - [Test with integ profile](#test-with-integ-profile)
      - [Test with admin profile](#test-with-admin-profile)
  - [Conclusion](#conclusion)
  - [cleanup](#cleanup-5)
- [IAM Roles for Service Accounts](#iam-roles-for-service-accounts)
  - [Preparation](#preparation)
    - [Enabling IAM Roles for Service Accounts on your Cluster](#enabling-iam-roles-for-service-accounts-on-your-cluster)
    - [Retrieve OpenID Connect issuer URL](#retrieve-openid-connect-issuer-url)
  - [Create an OpenID Connect Provider](#create-an-openid-connect-provider)
    - [Create your IAM OIDC Identity Provider for your cluster](#create-your-iam-oidc-identity-provider-for-your-cluster)
  - [Creating an IAM role for service account](#creating-an-iam-role-for-service-account)
  - [Specifying an IAM role for Service Account](#specifying-an-iam-role-for-service-account)
  - [Deploy Sample POD](#deploy-sample-pod)
    - [List S3 buckets](#list-s3-buckets)
    - [List EC2 Instances](#list-ec2-instances)
  - [cleanup](#cleanup-6)
- [Security Groups for PODs](#security-groups-for-pods)
    - [Introduction](#introduction-1)
    - [Objectives](#objectives)
  - [Prerequisites](#prerequisites-1)
  - [Security Group Creation](#security-group-creation)
    - [Create and configure the security groups](#create-and-configure-the-security-groups)
  - [RDS Creation](#rds-creation)
    - [CNI Configuration](#cni-configuration)
  - [SecurityGroup Policy](#securitygroup-policy)
  - [PODs Deployment](#pods-deployment)
    - [Kubernetes secrets](#kubernetes-secrets)
    - [Deployments](#deployments)
    - [Green Pod](#green-pod)
    - [Red Pod](#red-pod)
    - [cleanup](#cleanup-7)
- [Securing cluster with network policies](#securing-cluster-with-network-policies)
  - [Create network policies using Calico](#create-network-policies-using-calico)
  - [Install Calico](#install-calico)
  - [Stars policy demo](#stars-policy-demo)
    - [Create Resources](#create-resources)
      - [Stars Namespace](#stars-namespace)
      - [Create a namespace called stars:](#create-a-namespace-called-stars)
    - [Default Pod-to-pod communication](#default-pod-to-pod-communication)
    - [Apply network policies](#apply-network-policies)
    - [Allow Directional Traffic](#allow-directional-traffic)
    - [cleanup](#cleanup-8)
- [Deploying Microservices to EKS Fargate](#deploying-microservices-to-eks-fargate)
  - [Creating a fargate profile](#creating-a-fargate-profile)
    - [Create a Fargate profile](#create-a-fargate-profile)
  - [Setting up the LB Controller](#setting-up-the-lb-controller)
    - [AWS Load Balancer Controller](#aws-load-balancer-controller)
    - [Helm](#helm-1)
    - [Create IAM OIDC provider](#create-iam-oidc-provider)
    - [Create an IAM policy](#create-an-iam-policy)
    - [Create a IAM role and ServiceAccount for the Load Balancer controller](#create-a-iam-role-and-serviceaccount-for-the-load-balancer-controller)
    - [Install the TargetGroupBinding CRDs](#install-the-targetgroupbinding-crds)
    - [Deploy the Helm chart from the Amazon EKS charts repo](#deploy-the-helm-chart-from-the-amazon-eks-charts-repo)
  - [Deploying Pods to Fargate](#deploying-pods-to-fargate)
    - [Deploy the sample application](#deploy-the-sample-application)
  - [Ingress](#ingress)
    - [Ingress](#ingress-1)
  - [cleanup](#cleanup-9)
- [Migrate workloads to EKS](#migrate-workloads-to-eks)
  - [Create the KIND cluster](#create-the-kind-cluster)
  - [Deploy Counter APp to KIND](#deploy-counter-app-to-kind)
  - [Expose Counter APP from KIND](#expose-counter-app-from-kind)
  - [Configure EKS Cluster](#configure-eks-cluster)
  - [Deploy Counter APP to EKS](#deploy-counter-app-to-eks)
  - [Deploying database to EKS](#deploying-database-to-eks)
  - [cleanup](#cleanup-10)
- [Deploy Jenkins](#deploy-jenkins)
  - [Codecommit repository, access and code](#codecommit-repository-access-and-code)
  - [Creating the Jenkins Service Account](#creating-the-jenkins-service-account)
  - [Deploy Jenkins](#deploy-jenkins-1)
    - [Install Jenkins](#install-jenkins)
  - [Logging In](#logging-in)
  - [Setup Multibranch Projects](#setup-multibranch-projects)
  - [cleanup](#cleanup-11)
- [Continuous Delivery with Spinnaker](#continuous-delivery-with-spinnaker)
  - [Spinnaker Overview](#spinnaker-overview)
    - [Spinnaker Architecture](#spinnaker-architecture)
    - [Spinnaker Concepts](#spinnaker-concepts)
  - [Install Spinnaker Operator](#install-spinnaker-operator)
    - [PreRequisites](#prerequisites)
    - [EKS cluster setup](#eks-cluster-setup)
    - [Install Spinnaker CRDs](#install-spinnaker-crds)
    - [Install Spinnaker Operator](#install-spinnaker-operator-1)
  - [Artifact Configuration](#artifact-configuration)
    - [Configure Spinnaker Release Version](#configure-spinnaker-release-version)
    - [Configure S3 Artifact](#configure-s3-artifact)
    - [Set up environment variables](#set-up-environment-variables)
    - [Configure persistentStorage](#configure-persistentstorage)
    - [Configure ECR Artifact](#configure-ecr-artifact)
    - [Create a configmap](#create-a-configmap)
    - [Add a sidecar for token refresh](#add-a-sidecar-for-token-refresh)
  - [Add EKS Account](#add-eks-account)
    - [Download the latest spinnaker-tools release](#download-the-latest-spinnaker-tools-release)
    - [Setup environment variables](#setup-environment-variables)
    - [Create the service account](#create-the-service-account)
    - [Configure EKS Account](#configure-eks-account)
  - [Install Spinnaker](#install-spinnaker)
    - [Install Spinnaker Service](#install-spinnaker-service)
    - [Test the setup on Spinnaker UI](#test-the-setup-on-spinnaker-ui)
    - [Create a test application](#create-a-test-application)
    - [Create a test pipeline](#create-a-test-pipeline)
  - [Testing Helm-based Pipeline](#testing-helm-based-pipeline)
    - [Spinnaker UI](#spinnaker-ui)
      - [Create application](#create-application)
      - [Create Pipeline](#create-pipeline)
      - [Setup Trigger](#setup-trigger)
      - [Setup Bake Stage](#setup-bake-stage)
    - [Setup Deploy Stage](#setup-deploy-stage)
    - [Test Deployment](#test-deployment)
    - [Get deploymet details](#get-deploymet-details)
  - [cleanup](#cleanup-12)
    - [Delete Spinnaker artifacts](#delete-spinnaker-artifacts)
    - [(Optional) Create Old Nodegroup](#optional-create-old-nodegroup)
    - [Delete Nodegroup](#delete-nodegroup)
- [Patching/Upgrading Your EKS Cluster](#patchingupgrading-your-eks-cluster)
  - [The Upgrade Process](#the-upgrade-process)
  - [Upgrade EKS COntrol Plane](#upgrade-eks-control-plane)
  - [Upgrade EKS COre Add-ons](#upgrade-eks-core-add-ons)
  - [Upgrade Managed Node Group](#upgrade-managed-node-group)
- [Conclusion](#conclusion-1)
  - [What we've accomplished?](#what-weve-accomplished)
- [Final cleanup](#final-cleanup)
  - [Undeploy the applications](#undeploy-the-applications)
  - [Delete the EKSCTL Cluster](#delete-the-eksctl-cluster)
  - [Cleanup the Workspace](#cleanup-the-workspace)
- [Useful Resources](#useful-resources)
  - [Tools for AWS Fargate and Amazon ECS](#tools-for-aws-fargate-and-amazon-ecs)
- [What to learn next?](#what-to-learn-next)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## EKS CLUSTER CREATION WORKFLOW
![Cluster Creation Workflow](imgs/1.svg "Cluster Creation Workflow")
Let's start with the first bullet point "Create EKS cluster".
- Here we're reducing all of the necessary steps to setup a Kubernetes cluster to one single bullet point.
- Rest of the steps are the same whether you use a DIY cluster or an EKS cluster.
- Creating an EKS cluster is for us simply running an command. You can compare this with the DIY steps, we've to bring up the ETCD nodes, control plane nodes, ETCD nodes will build a quoram and then the rest of the control place components will start working.
- Only then, the worker nodes can be added and services deployed.
- All of this happens by running a single command in EKS.
- What happens when you create your EKS cluster?
  ![Cluster Control Plane](imgs/2.svg "Control Plane")

- When you look at the EKS cluster you'll build, you'll not see the control plane nodes. You'll probably see the ENIs that are attached to different components of the cluster.

- EKS Architecture for Control Plane and Worker node communication
  ![EKS Architecture](imgs/3.svg "EKS Architecture")

- In the above architecture diagram, we use 2 VPCs
  - One that will consist of all of the control plane nodes (the one on the right)
  - The other will consist of all of the worker nodes.

- All of the communication happens using ENIs which makes it seem like the communication is all happening in the same VPC.

- The Network Load Balancer ensures that the communication between the Kubelet and the API server is appropriately distributed.

- Once your EKS cluster is ready, you get an API endpoint and you’d use [Kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/), community developed tool to interact with your cluster.
  ![High Level](imgs/4.svg "High Level")

- When you use the `kubectl` commands, the request essentially goes to the AWS managed EKS control plane and then distributes the traffic to the relevant worker nodes in the customer managed VPC.


# Getting Started
## Create an AWS Account
-  If you don’t already have an AWS account with Administrator access: [create one now by clicking here](https://aws.amazon.com/getting-started/)

- Once you have an AWS account, ensure you are following the remaining workshop steps as an IAM user with administrator access to the AWS account: [Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?#/users$new).

- Enter the user details:
  ![User Details](imgs/5.png "User Details")

- Attach the AdministratorAccess IAM Policy:
  ![User Details](imgs/6.png "User Details")

- Click to create the new user:
  ![User Details](imgs/7.png "User Details")

- Take note of the login URL and save:
  ![User Details](imgs/8.png "User Details")


## Create a Workspace
- Launch Cloud9 in your closest region:
  - Oregon: https://us-west-2.console.aws.amazon.com/cloud9/home?region=us-west-2
  - Ireland: https://eu-west-1.console.aws.amazon.com/cloud9/home?region=eu-west-1
  - Ohio: https://us-east-2.console.aws.amazon.com/cloud9/home?region=us-east-2
  - Singapore: https://ap-southeast-1.console.aws.amazon.com/cloud9/home?region=ap-southeast-1

- Select Create environment
- Name it eksworkshop, click Next.
- Choose t3.small for instance type, take all default values and click Create environment
- When it comes up, customize the environment by:

  - Closing the Welcome tab
  ![Cloud9](imgs/cloud9-1.png "Cloud9")

- Opening a new terminal tab in the main work area
  ![Cloud9](imgs/cloud9-2.png "Cloud9")

- Closing the lower work area
  ![Cloud9](imgs/cloud9-3.png "Cloud9")
- Your workspace should now look like this
  ![Cloud9](imgs/cloud9-4.png "Cloud9")

- Increase the disk size on the Cloud9 instance.
  - __The following command adds more disk space to the root volume of the EC2 instance that Cloud9 runs on. Once the command completes, we reboot the instance and it could take a minute or two for the IDE to come back online.__
  ```python
  pip3 install --user --upgrade boto3
  export instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
  python -c "import boto3
  import os
  from botocore.exceptions import ClientError
  ec2 = boto3.client('ec2')
  volume_info = ec2.describe_volumes(
    Filters=[
        {
            'Name': 'attachment.instance-id',
            'Values': [
                os.getenv('instance_id')
            ]
        }
    ]
  )
  volume_id = volume_info['Volumes'][0]['VolumeId']
  try:
    resize = ec2.modify_volume(    
            VolumeId=volume_id,    
            Size=30
    )
    print(resize)
  except ClientError as e:
    if e.response['Error']['Code'] == 'InvalidParameterValue':
        print('ERROR MESSAGE: {}'.format(e))"
  if [ $? -eq 0 ]; then
    sudo reboot
  fi

  ```

## Install Kubernetes Tools
- Install kubectl
```
sudo curl --silent --location -o /usr/local/bin/kubectl \
   https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl
```

- Update awscli
Upgrade AWS CLI according to guidance in [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html).
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
- Install jq, envsubst (from GNU gettext utilities) and bash-completion
```
sudo yum -y install jq gettext bash-completion moreutils
```

- Install yq for yaml processing
```
echo 'yq() {
  docker run --rm -i -v "${PWD}":/workdir mikefarah/yq "$@"
}' | tee -a ~/.bashrc && source ~/.bashrc
```

- Verify the binaries are in the path and executable
```
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```

- Enable kubectl bash_completion
```
kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

- Set the AWS Load Balancer Controller version
```
echo 'export LBC_VERSION="v2.2.0"' >>  ~/.bash_profile
.  ~/.bash_profile
```


## Create an IAM role for your Workspace
- Follow [this](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess&roleName=eksworkshop-admin) deep link to create an IAM role with Administrator access.
- Confirm that `AWS service` and `EC2` are selected, then click `Next: Permissions` to view permissions.
- Confirm that `AdministratorAccess` is checked, then click `Next: Tags` to assign tags.
- Take the defaults, and click `Next: Review` to review.
- Enter __eksworkshop-admin__ for the Name, and click `Create role`
  ![Create Role](imgs/createrole.png "Create Role")


## Attach the IAM role to your Workspace
- Click the grey circle button (in top right corner) and select `Manage EC2 Instance.`
  ![Cloud9 Role](imgs/cloud9-role.png "Create Role")

- Select the instance, then choose `Actions / Security / Modify IAM Role`
  ![Cloud9 Role](imgs/c9instancerole.png "Create Role")

- Choose `eksworkshop-admin` from the` IAM Role` drop down, and select `Save`
  ![Cloud9 Attach Role](imgs/c9attachrole.png "Create Role")

## Update IAM Settings for your Workspace
Cloud9 normally manages IAM credentials dynamically. This isn’t currently compatible with the EKS IAM authentication, so we will disable it and rely on the IAM role instead.

- Return to your Cloud9 workspace and click the gear icon (in top right corner)
- Select `AWS SETTINGS`
- Turn off `AWS managed temporary credentials`
- Close the Preferences tab
  ![Cloud9 Disable IAM role Role](imgs/c9disableiam.png "Create Role")

- To ensure temporary credentials aren’t already in place we will also remove any existing credentials file:
```
rm -vf ${HOME}/.aws/credentials
```
We should configure our aws cli with our current region as default.
```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

- Check if AWS_REGION is set to desired region
```
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```

- Let’s save these into bash_profile
```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```
- Validate the IAM role
Use the [GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLI command to validate that the Cloud9 IDE is using the correct IAM role.
```
aws sts get-caller-identity --query Arn | grep eksworkshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

__If the IAM role is not valid, DO NOT PROCEED. Go back and confirm the steps on this page.__

## Clone the service repos
```
cd ~/environment
git clone https://github.com/aws-containers/ecsdemo-frontend.git
git clone https://github.com/saikiranchalla1/ecsdemo-nodejs.git
git clone https://github.com/saikiranchalla1/ecsdemo-crystal.git
```

Also clone the `learning-eks` repo onto the Cloud9 IDE. You'll need a Personal Access Token (__PAT__) to clone this repository. Create one in your Github [settings page](https://github.com/settings/tokens).

```
git clone https://github.com/saikiranchalla1/learning-eks
```
__Since this is a private repo, you'll have to provide your login credentials to Github. The password will be the PAT you created__

## Create an AWS KMS Customer Managed Key
Create a CMK for the EKS cluster to use when encrypting your Kubernetes secrets:
```
aws kms create-alias --alias-name alias/eksworkshop --target-key-id $(aws kms create-key --query KeyMetadata.Arn --output text)
```

Let’s retrieve the ARN of the CMK to input into the create cluster command.
```
export MASTER_ARN=$(aws kms describe-key --key-id alias/eksworkshop --query KeyMetadata.Arn --output text)
```

We set the MASTER_ARN environment variable to make it easier to refer to the KMS key later.

Now, let’s save the MASTER_ARN environment variable into the bash_profile
```
echo "export MASTER_ARN=${MASTER_ARN}" | tee -a ~/.bash_profile
```

# Launch Using [EKSCTL](https://eksctl.io/)
[eksctl](https://eksctl.io/) is a tool jointly developed by AWS and [Weaveworks](https://weave.works/) that automates much of the experience of creating EKS clusters.

In this module, we will use eksctl to launch and configure our EKS cluster and nodes.

## Prerequisites
For this module, we need to download the eksctl binary:
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

Confirm the eksctl command works:
```
eksctl version
```

Enable eksctl bash-completion

```
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

## Launch EKS
__DO NOT PROCEED with this step unless you have validated the IAM role in use by the Cloud9 IDE. You will not be able to run the necessary kubectl commands in the later modules unless the EKS cluster is built using the IAM role.__

Challenge: How do I check the IAM role on the workspace?
Run `aws sts get-caller-identity` and validate that your Arn contains eksworkshop-adminand an Instance Id.
```
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/eksworkshop-admin/i-01234567890abcdef"
}
```
If you do not see the correct role, please go back and validate the IAM role for troubleshooting.

If you do see the correct role, proceed to next step to create an EKS cluster.

### Create an EKS Cluster
Create an eksctl deployment file (eksworkshop.yaml) use in creating your cluster using the following syntax:
```
cat << EOF > eksworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
  version: "1.19"

availabilityZones: ["${AZS[0]}", "${AZS[1]}", "${AZS[2]}"]

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3
  instanceType: t3.small
  ssh:
    enableSsm: true

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

Next, use the file you created as the input for the eksctl cluster creation.
__We are deliberatly launching at least one Kubernetes version behind the latest available on Amazon EKS. This allows you to perform the cluster upgrade lab.__

```
eksctl create cluster -f eksworkshop.yaml
```
__Launching EKS and all the dependencies will take approximately 15 minutes__


## Test the Cluster
Confirm your nodes:
```
kubectl get nodes # if we see our 3 nodes, we know we have authenticated correctly
```

Export the Worker Role Name for use throughout the workshop:
```
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

__Congratulations!__
You now have a fully working Amazon EKS Cluster that is ready to use! Before you move on to any other labs, make sure to complete the steps on the next page to update the EKS Console Credentials.

## Console Credentials
This step is optional, as nearly all of the workshop content is CLI-driven. But, if you’d like full access to your workshop cluster in the EKS console this step is recommended.

The EKS console allows you to see not only the configuration aspects of your cluster, but also to view Kubernetes cluster objects such as Deployments, Pods, and Nodes. For this type of access, the console IAM User or Role needs to be granted permission within the cluster.

By default, the credentials used to create the cluster are automatically granted these permissions. Following along in the workshop, you’ve created a cluster using temporary IAM credentials from within Cloud9. This means that you’ll need to add your AWS Console credentials to the cluster.

### Import your EKS Console credentials to your new cluster:
IAM Users and Roles are bound to an EKS Kubernetes cluster via a ConfigMap named `aws-auth`. We can use `eksctl` to do this with one command.

You’ll need to determine the correct credential to add for your AWS Console access. If you know this already, you can skip ahead to the `eksctl create iamidentitymapping` step below.

If you’ve built your cluster from Cloud9 as part of this tutorial, invoke the following within your environment to determine your IAM Role or User ARN.
```
c9builder=$(aws cloud9 describe-environment-memberships --environment-id=$C9_PID | jq -r '.memberships[].userArn')
if echo ${c9builder} | grep -q user; then
	rolearn=${c9builder}
        echo Role ARN: ${rolearn}
elif echo ${c9builder} | grep -q assumed-role; then
        assumedrolename=$(echo ${c9builder} | awk -F/ '{print $(NF-1)}')
        rolearn=$(aws iam get-role --role-name ${assumedrolename} --query Role.Arn --output text)
        echo Role ARN: ${rolearn}
fi
```

With your ARN in hand, you can issue the command to create the identity mapping within the cluster.
```
eksctl create iamidentitymapping --cluster eksworkshop-eksctl --arn ${rolearn} --group system:masters --username admin
```

Note that permissions can be restricted and granular but as this is a workshop cluster, you’re adding your console credentials as administrator.

Now you can verify your entry in the AWS auth map within the console.
```
kubectl describe configmap -n kube-system aws-auth
```

Now you’re all set to move on. For more information, check out the [EKS documentation](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html) on this topic.


# Deploy the Kubernetes Dashboard
In this module, we will deploy the official Kubernetes dashboard, and connect through our Cloud9 Workspace.
![Dashboard](imgs/dashboard.png "K8s Dashboard")

## Deploy the dasboard
The official Kubernetes dashboard is not deployed by default, but there are instructions in [the official documentation](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

We can deploy the dashboard with the following command:
```
export DASHBOARD_VERSION="v2.0.0"

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml
```

Since this is deployed to our private cluster, we need to access it via a proxy. kube-proxy is available to proxy our requests to the dashboard service. In your workspace, run the following command:
```
kubectl proxy --port=8080 --address=0.0.0.0 --disable-filter=true &
```

This will start the proxy, listen on port 8080, listen on all interfaces, and will disable the filtering of non-localhost requests.

This command will continue to run in the background of the current terminal’s session.

__We are disabling request filtering, a security feature that guards against XSRF attacks. This isn’t recommended for a production environment, but is useful for our dev environment.__

## Access the Dashboard
Now we can access the Kubernetes Dashboard

1. In your Cloud9 environment, click Tools / Preview / Preview Running Application
2. Scroll to the end of the URL and append:
```
/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

The Cloud9 Preview browser doesn’t appear to support the token authentication, so once you have the login screen in the cloud9 preview browser tab, press the Pop Out button to open the login screen in a regular browser tab, like below:
  ![Popout](imgs/popout.png "K8s Dashboard")

- Open a New Terminal Tab and enter
```
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```

- Copy the output of this command and then click the radio button next to Token then in the text field below paste the output from the last command.
  ![Popout](imgs/dashboard-connect.png "K8s Dashboard")

- Then press Sign In.

## Cleanup

Stop the proxy and delete the dashboard deployment
```
# kill proxy
pkill -f 'kubectl proxy --port=8080'

# delete dashboard
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml

unset DASHBOARD_VERSION
```

# Deploy the example Microservices
## Deploy our sample applications
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ecsdemo-nodejs
  labels:
    app: ecsdemo-nodejs
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ecsdemo-nodejs
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: ecsdemo-nodejs
    spec:
      containers:
      - image: brentley/ecsdemo-nodejs:latest
        imagePullPolicy: Always
        name: ecsdemo-nodejs
        ports:
        - containerPort: 3000
          protocol: TCP
```
In the sample file above, we describe the service and how it should be deployed. We will write this description to the kubernetes api using kubectl, and kubernetes will ensure our preferences are met as the application is deployed.

The containers listen on port 3000, and native service discovery will be used to locate the running containers and communicate with them.

## Deploy NodeJS Backend api
Let’s bring up the NodeJS Backend API!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-nodejs
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-nodejs
```

## Deploy Crystal Backend API
Let’s bring up the Crystal Backend API!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-crystal
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

We can watch the progress by looking at the deployment status:

```
kubectl get deployment ecsdemo-crystal
```

## Let's check Service Types
Before we bring up the frontend service, let’s take a look at the service types we are using: This is kubernetes/service.yaml for our frontend service:

```
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-frontend
spec:
  selector:
    app: ecsdemo-frontend
  type: LoadBalancer
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
```

Notice type: LoadBalancer: This will configure an ELB to handle incoming traffic to this service.

Compare this to kubernetes/service.yaml for one of our backend services:

```
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-nodejs
spec:
  selector:
    app: ecsdemo-nodejs
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
```

Notice there is no specific service type described. When we check [the kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) we find that the default type is ClusterIP. This Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster.

## Ensure the ELB Service Role Exists

In AWS accounts that have never created a load balancer before, it’s possible that the service role for ELB might not exist yet.

We can check for the role, and create it if it’s missing.

Copy/Paste the following commands into your Cloud9 workspace:
```
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```

## Deploy Frontend Service
Let’s bring up the Ruby Frontend!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-frontend
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml

```

We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-frontend
```

## Find the service address
Now that we have a running service that is `type: LoadBalancer` we need to find the ELB’s address. We can do this by using the `get services` operation of kubectl:
```
kubectl get service ecsdemo-frontend
```

Notice the field isn’t wide enough to show the FQDN of the ELB. We can adjust the output format with this command:
```
kubectl get service ecsdemo-frontend -o wide
```

If we wanted to use the data programatically, we can also output via json. This is an example of how we might be able to make use of json output:
```
ELB=$(kubectl get service ecsdemo-frontend -o json | jq -r '.status.loadBalancer.ingress[].hostname')

curl -m3 -v $ELB
```

__It will take several minutes for the ELB to become healthy and start passing traffic to the frontend pods.__

You should also be able to copy/paste the loadBalancer hostname into your browser and see the application running. Keep this tab open while we scale the services up in the next module.

## Scale the Backend Services
When we launched our services, we only launched one container of each. We can confirm this by viewing the running pods:
```
kubectl get deployments
```

Now let’s scale up the backend services:
```
kubectl scale deployment ecsdemo-nodejs --replicas=3
kubectl scale deployment ecsdemo-crystal --replicas=3
```
Confirm by looking at deployments again:
```
kubectl get deployments
```

Also, check the browser tab where we can see our application running. You should now see traffic flowing to multiple backend services.

## Scale the Frontend
Let’s also scale our frontend service!
```
kubectl get deployments
kubectl scale deployment ecsdemo-frontend --replicas=3
kubectl get deployments
```

Check the browser tab where we can see our application running. You should now see traffic flowing to multiple frontend services.

## Cleanup the applications
To delete the resources created by the applications, we should delete the application deployments:

Undeploy the applications:
```
cd ~/environment/ecsdemo-frontend
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-crystal
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-nodejs
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml
```

# Helm
Helm is a package manager for Kubernetes that packages multiple Kubernetes resources into a single logical deployment unit called a Chart. Charts are easy to create, version, share, and publish.

In this module, we’ll cover installing Helm. Once installed, we’ll demonstrate how Helm can be used to deploy a simple nginx webserver, and a more sophisticated microservice.

![Helm](imgs/helm-logo.svg "Helm")

## Introduction
[Helm](https://helm.sh/) is a package manager and application management tool for Kubernetes that packages multiple Kubernetes resources into a single logical deployment unit called a Chart.

Helm helps you to:

- Achieve a simple (one command) and repeatable deployment
- Manage application dependency, using specific versions of other application and services
- Manage multiple deployment configurations: test, staging, production and others
- Execute post/pre deployment jobs during application deployment
- Update/rollback and test application deployments

## Install Helm CLI
Before we can get started configuring Helm, we’ll need to first install the command line tools that you will interact with. To do this, run the following:
```
curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

We can verify the version
```
helm version --short
```

Let’s configure our first Chart repository. Chart repositories are similar to APT or yum repositories that you might be familiar with on Linux, or Taps for Homebrew on macOS.

Download the stable repository so we have something to start with:
```
helm repo add stable https://charts.helm.sh/stable
```

Once this is installed, we will be able to list the charts you can install:
```
helm search repo stable
```
Finally, let’s configure Bash completion for the helm command:

```
helm completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
source <(helm completion bash)
```

## Deploy nginx with Helm
### Update the Chart repository
Helm uses a packaging format called Charts. A Chart is a collection of files and templates that describes Kubernetes resources.

Charts can be simple, describing something like a standalone web server (which is what we are going to create), but they can also be more complex, for example, a chart that represents a full web application stack, including web servers, databases, proxies, etc.

Instead of installing Kubernetes resources manually via kubectl, one can use Helm to install pre-defined Charts faster, with less chance of typos or other operator errors.

Chart repositories change frequently due to updates and new additions. To keep Helm’s local list updated with all these changes, we need to occasionally run the repository update command.

To update Helm’s local list of Charts, run:

```
# first, add the default repository, then update
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

And you should see something similar to:

```
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

Next, we’ll search for the `nginx` web server Chart.

### Search Chart repositories
Now that our repository Chart list has been updated, we can [search for Charts](https://helm.sh/docs/helm/helm_search/).

To list all Charts:
```
helm search repo
```
That should output something similar to:

```
NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION
stable/acs-engine-autoscaler            2.2.2           2.1.1                           Scales worker...
stable/aerospike                        0.3.2           v4.5.0.5                        A Helm chart...
...
```

You can see from the output that it dumped the list of all Charts we have added. In some cases that may be useful, but an even more useful search would involve a keyword argument. So next, we’ll search just for nginx:
```
helm search repo nginx
```

That results in:

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
stable/nginx-ingress            1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller ...
stable/nginx-ldapauth-proxy     0.1.6           1.13.5          DEPRECATED - nginx proxy with ldapauth ...
stable/nginx-lego               0.3.1                           Chart for...
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop...
...
```

This new list of Charts are specific to nginx, because we passed the nginx argument to the `helm search repo` command.

Further information on the command can be found [here](https://helm.sh/docs/helm/helm_search_repo/).


### Add the Bitnami Repository
In the last slide, we saw that `nginx` offers many different products via the default Helm Chart repository, but the nginx standalone web server is not one of them.

After a quick web search, we discover that there is a Chart for the nginx standalone web server available via the [Bitnami Chart repository](https://github.com/bitnami/charts).

To add the Bitnami Chart repo to our local list of searchable charts:
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Once that completes, we can search all Bitnami Charts:
```
helm search repo bitnami
```

Which results in:

```
NAME                     CHART VERSION   APP VERSION             DESCRIPTION
bitnami/bitnami-common   0.0.9           0.0.9           DEPRECATED Chart with custom templates used in ...
bitnami/airflow          10.2.5          2.1.2           Apache Airflow is a platform to programmaticall...
bitnami/apache           8.5.8           2.4.48          Chart for Apache HTTP Server                      
...
```

Search once again for nginx
```
helm search repo nginx
```

Now we are seeing more nginx options, across both repositories:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           9.3.7           1.21.1          Chart for the nginx server                        
bitnami/nginx-ingress-controller        7.6.16          0.48.1          Chart for the nginx Ingress controller            
stable/nginx-ingress                    1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller that us...
```

Or even search the Bitnami repo, just for nginx:
```
helm search repo bitnami/nginx
```

Which narrows it down to nginx on Bitnami:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           9.3.7           1.21.1          Chart for the nginx server            
bitnami/nginx-ingress-controller        7.6.16          0.48.1          Chart for the nginx Ingress controller
```

In both of those last two searches, we see

```
bitnami/nginx
```

as a search result. That’s the one we’re looking for, so let’s use Helm to install it to the EKS cluster.

### Install bitname/nginx
Installing the Bitnami standalone nginx web server Chart involves us using the [helm install](https://helm.sh/docs/helm/helm_install/) command.

A Helm Chart can be installed multiple times inside a Kubernetes cluster. This is because each installation of a Chart can be customized to suit a different purpose.

For this reason, you must supply a unique name for the installation, or ask Helm to generate a name for you.

Challenge:
How can you use Helm to deploy the bitnami/nginx chart?

HINT: Use the helm utility to install the bitnami/nginx chart and specify the name mywebserver for the Kubernetes deployment. Consult the helm install documentation or run the helm install --help command to figure out the syntax.

Solution:
```
helm install mywebserver bitnami/nginx
```

Once you run this command, the output will contain the information about the deployment status, revision, namespace, etc, similar to:

```
NAME: mywebserver
LAST DEPLOYED: Thu Jul 15 13:52:34 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

NGINX can be accessed through the following DNS name from within your cluster:

    mywebserver-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w mywebserver-nginx'

    export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services mywebserver-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace default mywebserver-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"
```

In order to review the underlying Kubernetes services, pods and deployments, run:
```
kubectl get svc,po,deploy
```
__In the following kubectl command examples, it may take a minute or two for each of these objects' DESIRED and CURRENT values to match; if they don’t match on the first try, wait a few seconds, and run the command again to check the status.__

The first object shown in this output is a Deployment. A Deployment object manages rollouts (and rollbacks) of different versions of an application.

You can inspect this Deployment object in more detail by running the following command:
```
kubectl describe deployment mywebserver
```

The next object shown created by the Chart is a Pod. A Pod is a group of one or more containers.

To verify the Pod object was successfully deployed, we can run the following command:
```
kubectl get pods -l app.kubernetes.io/name=nginx
```

And you should see output similar to:

```
NAME                                 READY     STATUS    RESTARTS   AGE
mywebserver-nginx-85985c8466-tczst   1/1       Running   0          10s
```

The third object that this Chart creates for us is a Service. A Service enables us to contact this nginx web server from the Internet, via an Elastic Load Balancer (ELB).

To get the complete URL of this Service, run:
```
kubectl get service mywebserver-nginx -o wide
```

That should output something similar to:

```
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP
mywebserver-nginx   LoadBalancer   10.100.223.99   abc123.amazonaws.com
```

Copy the value for EXTERNAL-IP, open a new tab in your web browser, and paste it in.

__It may take a couple minutes for the ELB and its associated DNS name to become available; if you get an error, wait one minute, and hit reload.__

When the Service does come online, you should see a welcome message similar to:

![Welcome to Nginx](imgs/welcome_to_nginx.png "Welcome to Nginx")

Congratulations! You’ve now successfully deployed the nginx standalone web server to your EKS cluster!

### cleanup
To remove all the objects that the Helm Chart created, we can use [Helm uninstall](https://helm.sh/docs/helm/helm_uninstall/).

Before we uninstall our application, we can verify what we have running via the [Helm list](https://helm.sh/docs/helm/helm_list/) command:

```
helm list
```

You should see output similar to below, which show that mywebserver is installed:

```
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
mywebserver     default         1               2021-07-15 13:52:34.563653342 +0000 UTC deployed        nginx-9.3.7     1.21.1   
```

It was a lot of fun; we had some great times sending HTTP back and forth, but now its time to uninstall this deployment. To uninstall:
```
helm uninstall mywebserver
```
And you should be met with the output:

```
release "mywebserver" uninstalled
```

kubectl will also demonstrate that our pods and service are no longer available:
```
kubectl get pods -l app.kubernetes.io/name=nginx
kubectl get service mywebserver-nginx -o wide
```
As would trying to access the service via the web browser via a page reload.

With that, cleanup is complete.


## Deploy Example Microservices using Helm
In this module, we will demonstrate how to deploy microservices using a custom Helm Chart, instead of doing everything manually using kubectl.

For detailed information on working with chart templates, refer to the Helm [docs](https://docs.helm.sh/chart_template_guide/).

### Create a Chart
Helm charts have a structure similar to:

```
/eksdemo
├── charts/
├── Chart.yaml
├── templates/
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

We’ll follow this template, and create a new chart called eksdemo with the following commands:
```
cd ~/environment
helm create eksdemo
cd eksdemo
```

### Customize Defaults
If you look in the newly created eksdemo directory, you’ll see several files and directories.

The table below outlines the purpose of each component in the Helm chart structure.

|File or Directory|	Description|
|-----------------|------------|
|charts/ |	Sub-charts that the chart depends on|
|Chart.yaml	|Information about your chart|
|values.yaml|	The default values for your templates|
|template/|	The template files|
|template/deployment.yaml|	Basic manifest for creating Kubernetes Deployment objects|
|template/_helpers.tpl|	Used to define Go template helpers|
|template/hpa.yaml |	Basic manifest for creating Kubernetes Horizontal Pod Autoscaler objects|
|template/ingress.yaml |	Basic manifest for creating Kubernetes Ingress objects|
|template/NOTES.txt|	A plain text file to give users detailed information about how to use the newly installed chart|
|template/serviceaccount.yaml |	Basic manifest for creating Kubernetes ServiceAccount objects|
|template/service.yaml|	Basic manifest for creating Kubernetes Service objects|
|tests/	| Directory of Test files |
| tests/test-connections.yaml	 | Tests that validate that your chart works as expected when it is installed|

We’re actually going to create our own files, so we’ll delete these boilerplate files
```
rm -rf ~/environment/eksdemo/templates/
rm ~/environment/eksdemo/Chart.yaml
rm ~/environment/eksdemo/values.yaml
```

Run the following code block to create a new Chart.yaml file which will describe the chart
```
cat <<EoF > ~/environment/eksdemo/Chart.yaml
apiVersion: v2
name: eksdemo
description: A Helm chart for EKS Workshop Microservices application
version: 0.1.0
appVersion: 1.0
EoF
```

Next we’ll copy the manifest files for each of our microservices into the templates directory as servicename.yaml
```
#create subfolders for each template type
mkdir -p ~/environment/eksdemo/templates/deployment
mkdir -p ~/environment/eksdemo/templates/service

# Copy and rename frontend manifests
cp ~/environment/ecsdemo-frontend/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/frontend.yaml
cp ~/environment/ecsdemo-frontend/kubernetes/service.yaml ~/environment/eksdemo/templates/service/frontend.yaml

# Copy and rename crystal manifests
cp ~/environment/ecsdemo-crystal/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/crystal.yaml
cp ~/environment/ecsdemo-crystal/kubernetes/service.yaml ~/environment/eksdemo/templates/service/crystal.yaml

# Copy and rename nodejs manifests
cp ~/environment/ecsdemo-nodejs/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/nodejs.yaml
cp ~/environment/ecsdemo-nodejs/kubernetes/service.yaml ~/environment/eksdemo/templates/service/nodejs.yaml
```


All files in the templates directory are sent through the template engine. These are currently plain YAML files that would be sent to Kubernetes as-is.

#### Replace hard-coded values with template directives

Let’s replace some of the values with template directives to enable more customization by removing hard-coded values.

Open ~/environment/eksdemo/templates/deployment/frontend.yaml in your Cloud9 editor.

__The following steps should be completed seperately for frontend.yaml, crystal.yaml, and nodejs.yaml.__

Under `spec`, find __replicas: 1__ and replace with the following:

```
replicas: {{ .Values.replicas }}
```

Under `spec.template.spec.containers.image`, replace the image with the correct template value from the table below:

|Filename |	Value|
|---------|------|
|frontend.yaml|	- image: {{ .Values.frontend.image }}:{{ .Values.version }}|
|crystal.yaml |	- image: {{ .Values.crystal.image }}:{{ .Values.version }}|
|nodejs.yaml|	- image: {{ .Values.nodejs.image }}:{{ .Values.version }}|

#### Create a values.yaml file with our template defaults

Run the following code block to populate our template directives with default values.
```
cat <<EoF > ~/environment/eksdemo/values.yaml
# Default values for eksdemo.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

# Release-wide Values
replicas: 3
version: 'latest'

# Service Specific Values
nodejs:
  image: brentley/ecsdemo-nodejs
crystal:
  image: brentley/ecsdemo-crystal
frontend:
  image: brentley/ecsdemo-frontend
EoF
```

### Deploy the EKSDemo Chart
#### Use the dry-run flag to test our templates
To test the syntax and validity of the Chart without actually deploying it, we’ll use the `--dry-run` flag.

The following command will build and output the rendered templates without installing the Chart:
```
helm install --debug --dry-run workshop ~/environment/eksdemo
```

Confirm that the values created by the template look correct.

#### Deploy the chart
Now that we have tested our template, let’s install it.
```
helm install workshop ~/environment/eksdemo
```

Similar to what we saw previously in the nginx Helm Chart example, an output of the command will contain the information about the deployment status, revision, namespace, etc, similar to:

```
NAME: workshop
LAST DEPLOYED: Sat Jul 17 08:47:32 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

In order to review the underlying services, pods and deployments, run:
```
kubectl get svc,po,deploy
```

### Testing the service
To test the service our eksdemo Chart created, we’ll need to get the name of the ELB endpoint that was generated when we deployed the Chart:
```
kubectl get svc ecsdemo-frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}"; echo
```

Copy that address, and paste it into a new tab in your browser. You should see something similar to:

![EKS Demo UI](imgs/micro_example.png "Cluster Creation Workflow")

### Rolling Back
Mistakes will happen during deployment, and when they do, Helm makes it easy to undo, or “roll back” to the previously deployed version.

#### Update the demo application chart with a breaking change
Open `values.yaml` and modify the image name under `nodejs.image` to brentley/ecsdemo-nodejs-non-existing. This image does not exist, so this will break our deployment.

#### Deploy the updated demo application chart:
```
helm upgrade workshop ~/environment/eksdemo
```

The rolling upgrade will begin by creating a new nodejs pod with the new image. The new `ecsdemo-nodejs` Pod should fail to pull non-existing image. Run `kubectl get pods` to see the `ImagePullBackOff` error.

```
kubectl get pods
```
```
NAME                                READY   STATUS             RESTARTS   AGE
ecsdemo-crystal-56976b4dfd-9f2rf    1/1     Running            0          2m10s
ecsdemo-frontend-7f5ddc5485-8vqck   1/1     Running            0          2m10s
ecsdemo-nodejs-56487f6c95-mv5xv     0/1     ImagePullBackOff   0          6s
ecsdemo-nodejs-58977c4597-r6hvj     1/1     Running            0          2m10s
```

Run helm status workshop to verify the __LAST DEPLOYED__ timestamp.

```
helm status workshop
```
```

NAME: workshop
LAST DEPLOYED: Fri Jul 16 13:53:22 2021
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
...
```

This should correspond to the last entry on `helm history workshop`

```
helm history workshop
```

#### Rollback the failed upgrade
Now we are going to rollback the application to the previous working release revision.

First, list Helm release revisions:
```
helm history workshop
```
Then, rollback to the previous application revision (can rollback to any revision too):
```
# rollback to the 1st revision
helm rollback workshop 1
```

Validate workshop release status and you will see a new revision that is based on the rollback.

```
helm status workshop
```
```
NAME: workshop
LAST DEPLOYED: Fri Jul 16 13:55:27 2021
NAMESPACE: default
STATUS: deployed
REVISION: 3
TEST SUITE: None
```

Verify that the error is gone

```
kubectl get pods
```
```
NAME                                READY   STATUS    RESTARTS   AGE
ecsdemo-crystal-56976b4dfd-9f2rf    1/1     Running   0          6m
ecsdemo-frontend-7f5ddc5485-8vqck   1/1     Running   0          6m
ecsdemo-nodejs-58977c4597-r6hvj     1/1     Running   0          6m
```

### cleanup
To delete the workshop release, run:

```
helm uninstall workshop
```

# Health Checks
By default, Kubernetes will restart a container if it crashes for any reason. It uses Liveness and Readiness probes which can be configured for running a robust application by identifying the healthy containers to send traffic to and restarting the ones when required.

In this section, we will understand how [liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) are defined and test the same against different states of a pod. Below is the high level description of how these probes work.

__Liveness probes__ are used in Kubernetes to know when a pod is alive or dead. A pod can be in a dead state for a variety of reasons; Kubernetes will kill and recreate the pod when a liveness probe does not pass.

__Readiness probes__ are used in Kubernetes to know when a pod is ready to serve traffic. Only when the readiness probe passes will a pod receive traffic from the service; if a readiness probe fails traffic will not be sent to the pod.

We will review some examples in this module to understand different options for configuring liveness and readiness probes.

## Configure Liveness Probe
### Configure the Probe
Use the command below to create a directory
```
mkdir -p ~/environment/healthchecks
```

Run the following code block to populate the manifest file __~/environment/healthchecks/liveness-app.yaml__. In the configuration file, the `livenessProbe` field determines how `kubelet` should check the container in order to consider whether it is healthy or not. `kubelet` uses the periodSeconds field to do frequent check on the Container. In this case, `kubelet` checks the liveness probe every 5 seconds. The `initialDelaySeconds` field is used to tell kubelet that it should wait for 5 seconds before doing the first probe. To perform a probe, `kubelet` sends a HTTP GET request to the server hosting this pod and if the handler for the servers` /health` returns a success code, then the container is considered healthy. If the handler returns a failure code, the `kubelet` kills the container and restarts it.
```
cat <<EoF > ~/environment/healthchecks/liveness-app.yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-app
spec:
  containers:
  - name: liveness
    image: brentley/ecsdemo-nodejs
    livenessProbe:
      httpGet:
        path: /health
        port: 3000
      initialDelaySeconds: 5
      periodSeconds: 5
EoF
```

Let’s create the pod using the manifest:

```
kubectl apply -f ~/environment/healthchecks/liveness-app.yaml
```

The above command creates a pod with liveness probe.
```
kubectl get pod liveness-app
```

The output looks like below. Notice the __RESTARTS__

```
NAME           READY     STATUS    RESTARTS   AGE
liveness-app   1/1       Running   0          11s
```

The `kubectl describe` command will show an event history which will show any probe failures or restarts.

```
kubectl describe pod liveness-app
```
```
Events:
  Type    Reason                 Age   From                                    Message
  ----    ------                 ----  ----                                    -------
  Normal  Scheduled              38s   default-scheduler                       Successfully assigned default/liveness-app to ip-192-168-18-63.ec2.internal
  Normal  Pulling                37s   kubelet                                 Pulling image "brentley/ecsdemo-nodejs"
  Normal  Pulled                 37s   kubelet                                 Successfully pulled image "brentley/ecsdemo-nodejs" in 108.556215ms
  Normal  Created                37s   kubelet                                 Created container liveness
  Normal  Started                37s   kubelet                                 Started container liveness
```

### Introduce a Failure
We will run the next command to send a SIGUSR1 signal to the nodejs application. By issuing this command we will send a kill signal to the application process in the docker runtime.

```
kubectl exec -it liveness-app -- /bin/kill -s SIGUSR1 1
```

Describe the pod after waiting for 15-20 seconds and you will notice the kubelet actions of killing the container and restarting it.

```
Events:
  Type     Reason         Age                From                  Message
  ----     ------         ----               ----                  -------
  Normal   Scheduled      72s                default-scheduler     Successfully assigned default/liveness-app to ip-192-168-18-63.ec2.internal
  Normal   Pulled         71s                kubelet               Successfully pulled image "brentley/ecsdemo-nodejs" in 100.615806ms
  Warning  Unhealthy      37s (x3 over 47s)  kubelet               Liveness probe failed: Get http://192.168.13.176:3000/health: context deadline exceeded (Client.Timeout exceeded while awaiting headers)
  Normal   Killing        37s                kubelet               Container liveness failed liveness probe, will be restarted
  Normal   Pulling        6s (x2 over 71s)   kubelet               Pulling image "brentley/ecsdemo-nodejs"
  Normal   Created        6s (x2 over 71s)   kubelet               Created container liveness
  Normal   Started        6s (x2 over 71s)   kubelet               Started container liveness
  Normal   Pulled         6s                 kubelet               Successfully pulled image "brentley/ecsdemo-nodejs" in 118.19123ms
```

When the nodejs application entered a debug mode with SIGUSR1 signal, it did not respond to the health check pings and kubelet killed the container. The container was subject to the default restart policy.

```
kubectl get pod liveness-app
```

The output looks like below:

```
NAME           READY     STATUS    RESTARTS   AGE
liveness-app   1/1       Running   1          12m
```

### Challenge:
How can we check the status of the container health checks?
Solution: `kubectl logs liveness-app`

## Configure Readiness probe
### Configure the Probe
Run the following code block to populate `~/environment/healthchecks/readiness-deployment.yaml`. The readinessProbe definition explains how a linux command can be configured as `healthcheck`. We create an empty file `/tmp/healthy` to configure readiness probe and use the same to understand how `kubelet` helps to update a deployment with only healthy pods.
```
cat <<EoF > ~/environment/healthchecks/readiness-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: readiness-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: readiness-deployment
  template:
    metadata:
      labels:
        app: readiness-deployment
    spec:
      containers:
      - name: readiness-deployment
        image: alpine
        command: ["sh", "-c", "touch /tmp/healthy && sleep 86400"]
        readinessProbe:
          exec:
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5
          periodSeconds: 3
EoF
```

We will now create a deployment to test readiness probe:
```
kubectl apply -f ~/environment/healthchecks/readiness-deployment.yaml
```

The above command creates a deployment with 3 replicas and readiness probe as described in the beginning.
```
kubectl get pods -l app=readiness-deployment
```
The output looks similar to below:


```
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   1/1       Running   0          31s
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          31s
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          31s
```

Let us also confirm that all the replicas are available to serve traffic when a service is pointed to this deployment.
```
kubectl describe deployment readiness-deployment | grep Replicas:
```

The output looks like below:

```
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
```

### Introduce a Failure
Pick one of the pods from above 3 and issue a command as below to delete the /tmp/healthy file which makes the readiness probe fail.

```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- rm /tmp/healthy
```

`readiness-deployment-7869b5d679-922mx` was picked in our example cluster. The `/tmp/healthy` file was deleted. This file must be present for the readiness check to pass. Below is the status after issuing the command.

```
kubectl get pods -l app=readiness-deployment
```

The output looks similar to below:

```
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   0/1       Running   0          4m
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          4m
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          4m
```

Traffic will not be routed to the first pod in the above deployment. The ready column confirms that the readiness probe for this pod did not pass and hence was marked as not ready.

We will now check for the replicas that are available to serve traffic when a service is pointed to this deployment.

```
kubectl describe deployment readiness-deployment | grep Replicas:
```

The output looks like below:

```
Replicas:               3 desired | 3 updated | 3 total | 2 available | 1 unavailable
```

When the readiness probe for a pod fails, the endpoints controller removes the pod from list of endpoints of all services that match the pod.

### Challenge:
How would you restore the pod to Ready status?
Solution: Run the below command with the name of the pod to recreate the __/tmp/healthy__ file. Once the pod passes the probe, it gets marked as ready and will begin to receive traffic again.
```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- touch /tmp/healthy
kubectl get pods -l app=readiness-deployment
```

## cleanup
Our Liveness Probe example used HTTP request and Readiness Probe executed a command to check health of a pod. Same can be accomplished using a TCP request as described in the [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).

```
kubectl delete -f ~/environment/healthchecks/liveness-app.yaml
kubectl delete -f ~/environment/healthchecks/readiness-deployment.yaml
```

# Autoscaling our Applications and Cluster
In this module, we will show patterns for scaling your worker nodes and applications deployments automatically.

Automatic scaling in K8s comes in two forms:

__Horizontal Pod Autoscaler (HPA)__ scales the pods in a deployment or replica set. It is implemented as a K8s API resource and a controller. The controller manager queries the resource utilization against the metrics specified in each HorizontalPodAutoscaler definition. It obtains the metrics from either the resource metrics API (for per-pod resource metrics), or the custom metrics API (for all other metrics).

__Cluster Autoscaler (CA)__ a component that automatically adjusts the size of a Kubernetes Cluster so that all pods have a place to run and there are no unneeded nodes.

## Install kube-ops-view
Before starting to learn about the various auto-scaling options for your EKS cluster we are going to install [Kube-ops-view](https://github.com/hjacobs/kube-ops-view) from [Henning Jacobs](https://github.com/hjacobs).

Kube-ops-view provides a common operational picture for a Kubernetes cluster that helps with understanding our cluster setup in a visual way.

__We will deploy kube-ops-view using Helm configured in a previous module__

The following line updates the stable helm repository and then installs kube-ops-view using a LoadBalancer Service type and creating a RBAC (Resource Base Access Control) entry for the read-only service account to read nodes and pods information from the cluster.
```
helm install kube-ops-view \
stable/kube-ops-view \
--set service.type=LoadBalancer \
--set rbac.create=True
```

The execution above installs kube-ops-view exposing it through a Service using the LoadBalancer type. A successful execution of the command will display the set of resources created and will prompt some advice asking you to use `kubectl proxy` and a local URL for the service. Given we are using the type LoadBalancer for our service, we can disregard this; Instead we will point our browser to the external load balancer.

__Monitoring and visualization shouldn’t be typically be exposed publicly unless the service is properly secured and provide methods for authentication and authorization. You can still deploy kube-ops-view using a Service of type ClusterIP by removing the --set service.type=LoadBalancer section and using kubectl proxy. Kube-ops-view does also support Oauth 2__

To check the chart was installed successfully:
```
helm list
```

should display :
```
NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
kube-ops-view   1               Sun Sep 22 11:47:31 2019        DEPLOYED        kube-ops-view-1.1.0     0.11            default  
```

With this we can explore kube-ops-view output by checking the details about the newly service created.
```
kubectl get svc kube-ops-view | tail -n 1 | awk '{ print "Kube-ops-view URL = http://"$4 }'
```

This will display a line similar to `Kube-ops-view URL = http://<URL_PREFIX_ELB>.amazonaws.com `. Opening the URL in your browser will provide the current state of our cluster.

__You may need to refresh the page and clean your browser cache. The creation and setup of the LoadBalancer may take a few minutes; usually in two minutes you should see kub-ops-view.__

![Kube Ops View](imgs/kube-ops-view.png "kube-ops-view")

As this workshop moves along and you perform scale up and down actions, you can check the effects and changes in the cluster using kube-ops-view. Check out the different components and see how they map to the concepts that we have already covered during this workshop.

__Spend some time checking the state and properties of your EKS cluster.__

![Kube Ops View](imgs/kube-ops-view-legend.png "kube-ops-view")

## Configure Horizontal Pod Autoscaler (HPA)
### Deploy the Metrics Server
Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines.

These metrics will drive the scaling behavior of the [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

We will deploy the metrics server using [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server).

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.0/components.yaml
```

Lets' verify the status of the metrics-server APIService (it could take a few minutes).

```
kubectl get apiservice v1beta1.metrics.k8s.io -o json | jq '.status'
```

```
{
  "conditions": [
    {
      "lastTransitionTime": "2020-11-10T06:39:13Z",
      "message": "all checks passed",
      "reason": "Passed",
      "status": "True",
      "type": "Available"
    }
  ]
}
```

We are now ready to scale a deployed application

## Scale an application with HPA
### Deploy a Sample App
We will deploy an application and expose as a service on TCP port 80.

The application is a custom-built image based on the php-apache image. The index.php page performs calculations to generate CPU load. More information can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server).
```
kubectl create deployment php-apache --image=us.gcr.io/k8s-artifacts-prod/hpa-example
kubectl set resources deploy php-apache --requests=cpu=200m
kubectl expose deploy php-apache --port 80

kubectl get pod -l app=php-apache
```

### Create an HPA resource
This HPA scales up when CPU exceeds 50% of the allocated container resource.
```
kubectl autoscale deployment php-apache `#The target average CPU utilization` \
    --cpu-percent=50 \
    --min=1 `#The lower limit for the number of pods that can be set by the autoscaler` \
    --max=10 `#The upper limit for the number of pods that can be set by the autoscaler`
```

View the HPA using kubectl. You probably will see `<unknown>/50%` for 1-2 minutes and then you should be able to see `0%/50%`

```
kubectl get hpa
```

### Generate load to trigger scaling
Open a new terminal in the Cloud9 Environment and run the following command to drop into a shell on a new container
```
kubectl --generator=run-pod/v1 run -i --tty load-generator --image=busybox /bin/sh
```

Execute a while loop to continue getting http:///php-apache
```
while true; do wget -q -O - http://php-apache; done
```

In the previous tab, watch the HPA with the following command

```
kubectl get hpa -w
```

You will see HPA scale the pods from 1 up to our configured maximum (10) until the CPU average is below our target (50%)

![HPA Results](imgs/scaling-hpa-results.png "Scaling HPA")

You can now stop (Ctrl + C) load test that was running in the other terminal. You will notice that HPA will slowly bring the replica count to min number based on its configuration. You should also get out of load testing application by pressing Ctrl + D.

## Configure Cluster Autoscaler (CA)
Cluster Autoscaler for AWS provides integration with Auto Scaling groups. It enables users to choose from four different options of deployment:

- One Auto Scaling group
- Multiple Auto Scaling groups
- Auto-Discovery
- Control-plane Node setup

Auto-Discovery is the preferred method to configure Cluster Autoscaler. Click [here](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws) for more information.

Cluster Autoscaler will attempt to determine the CPU, memory, and GPU resources provided by an Auto Scaling Group based on the instance type specified in its Launch Configuration or Launch Template.

### Configure the ASG
You configure the size of your Auto Scaling group by setting the minimum, maximum, and desired capacity. When we created the cluster we set these settings to 3.
```
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

```
-------------------------------------------------------------
|                 DescribeAutoScalingGroups                 |
+-------------------------------------------+----+----+-----+
|  eks-1eb9b447-f3c1-0456-af77-af0bbd65bc9f |  2 |  4 |  3  |
+-------------------------------------------+----+----+-----+

```

Now, increase the maximum capacity to 4 instances

```
# we need the ASG name
export ASG_NAME=$(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].AutoScalingGroupName" --output text)

# increase max capacity up to 4
aws autoscaling \
    update-auto-scaling-group \
    --auto-scaling-group-name ${ASG_NAME} \
    --min-size 3 \
    --desired-capacity 3 \
    --max-size 4

# Check new values
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

### IAM roles for service accounts

With IAM roles for service accounts on Amazon EKS clusters, you can associate an IAM role with a [Kubernetes service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). This service account can then provide AWS permissions to the containers in any pod that uses that service account. With this feature, you no longer need to provide extended permissions to the node IAM role so that pods on that node can call AWS APIs.

Enabling IAM roles for service accounts on your cluster
```
eksctl utils associate-iam-oidc-provider \
    --cluster eksworkshop-eksctl \
    --approve
```

Creating an IAM policy for your service account that will allow your CA pod to interact with the autoscaling groups.
```
mkdir ~/environment/cluster-autoscaler

cat <<EoF > ~/environment/cluster-autoscaler/k8s-asg-policy.json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeAutoScalingInstances",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeTags",
                "autoscaling:SetDesiredCapacity",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "ec2:DescribeLaunchTemplateVersions"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
EoF

aws iam create-policy   \
  --policy-name k8s-asg-policy \
  --policy-document file://~/environment/cluster-autoscaler/k8s-asg-policy.json

```

Finally, create an IAM role for the cluster-autoscaler Service Account in the kube-system namespace.

```
eksctl create iamserviceaccount \
    --name cluster-autoscaler \
    --namespace kube-system \
    --cluster eksworkshop-eksctl \
    --attach-policy-arn "arn:aws:iam::${ACCOUNT_ID}:policy/k8s-asg-policy" \
    --approve \
    --override-existing-serviceaccounts
```

Make sure your service account with the ARN of the IAM role is annotated
```
kubectl -n kube-system describe sa cluster-autoscaler
```

Output

```
Name:                cluster-autoscaler
Namespace:           kube-system
Labels:              <none>
Annotations:         eks.amazonaws.com/role-arn: arn:aws:iam::197520326489:role/eksctl-eksworkshop-eksctl-addon-iamserviceac-Role1-12LNPCGBD6IPZ
Image pull secrets:  <none>
Mountable secrets:   cluster-autoscaler-token-vfk8n
Tokens:              cluster-autoscaler-token-vfk8n
Events:              <none>
```

### Deploy the Cluster Autoscaler (CA)
Deploy the Cluster Autoscaler to your cluster with the following command.
__The required YAML manifest file is in the `yamls` directory of the repository__

```
cd ~/environment/learning-eks
kubectl apply -f yamls/cluster-autoscaler-autodiscover.yaml
```

To prevent CA from removing nodes where its own pod is running, we will add the `cluster-autoscaler.kubernetes.io/safe-to-evict` annotation to its deployment with the following command

```
kubectl -n kube-system \
    annotate deployment.apps/cluster-autoscaler \
    cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

Finally let’s update the autoscaler image

```
# we need to retrieve the latest docker image available for our EKS version
export K8S_VERSION=$(kubectl version --short | grep 'Server Version:' | sed 's/[^0-9.]*\([0-9.]*\).*/\1/' | cut -d. -f1,2)
export AUTOSCALER_VERSION=$(curl -s "https://api.github.com/repos/kubernetes/autoscaler/releases" | grep '"tag_name":' | sed -s 's/.*-\([0-9][0-9\.]*\).*/\1/' | grep -m1 ${K8S_VERSION})

kubectl -n kube-system \
    set image deployment.apps/cluster-autoscaler \
    cluster-autoscaler=us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v${AUTOSCALER_VERSION}
```

Watch the logs
```
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

We are now ready to scale our cluster

Related files:
- [cluster-autoscaler-autodiscover.yaml](/yamls/cluster-autoscaler-autodiscover.yaml)

## Scale a cluster with CA
### Deploy a Sample App
We will deploy an sample `nginx` application as a `ReplicaSet` of 1 Pod

```
cat <<EoF> ~/environment/cluster-autoscaler/nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-to-scaleout
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        service: nginx
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx-to-scaleout
        resources:
          limits:
            cpu: 500m
            memory: 512Mi
          requests:
            cpu: 500m
            memory: 512Mi
EoF

kubectl apply -f ~/environment/cluster-autoscaler/nginx.yaml

kubectl get deployment/nginx-to-scaleout
```

### Scale our ReplicaSet

Let’s scale out the replicaset to 10

```
kubectl scale --replicas=10 deployment/nginx-to-scaleout
```

Some pods will be in the Pending state, which triggers the cluster-autoscaler to scale out the EC2 fleet.
```
kubectl get pods -l app=nginx -o wide --watch
```

```
NAME                                 READY     STATUS    RESTARTS   AGE

nginx-to-scaleout-7cb554c7d5-2d4gp   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-2nh69   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-45mqz   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-4qvzl   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-5jddd   1/1       Running   0          34s
nginx-to-scaleout-7cb554c7d5-5sx4h   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-5xbjp   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-6l84p   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-7vp7l   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-86pr6   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-88ttw   0/1       Pending   0          12s
```

View the cluster-autoscaler logs
```
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

You will notice Cluster Autoscaler events similar to below

![Scaling ASG](imgs/scaling-asg-up2.png "Scaling ASG")

Check the [EC2 AWS Management Console](https://console.aws.amazon.com/ec2/home?#Instances:sort=instanceId) to confirm that the Auto Scaling groups are scaling up to meet demand. This may take a few minutes. You can also follow along with the pod deployment from the command line. You should see the pods transition from pending to running as nodes are scaled up.

![Scaling ASG](imgs/scaling-asg-up.png "Scaling ASG")


or by using the kubectl

```
kubectl get nodes
```

Output

```
ip-192-168-12-114.us-east-2.compute.internal   Ready    <none>   3d6h   v1.17.7-eks-bffbac
ip-192-168-29-155.us-east-2.compute.internal   Ready    <none>   63s    v1.17.7-eks-bffbac
ip-192-168-55-187.us-east-2.compute.internal   Ready    <none>   3d6h   v1.17.7-eks-bffbac
ip-192-168-82-113.us-east-2.compute.internal   Ready    <none>   8h     v1.17.7-eks-bffbac
```

## cleanup
```
kubectl delete -f ~/environment/cluster-autoscaler/nginx.yaml

kubectl delete -f https://www.eksworkshop.com/beginner/080_scaling/deploy_ca.files/cluster-autoscaler-autodiscover.yaml

eksctl delete iamserviceaccount \
  --name cluster-autoscaler \
  --namespace kube-system \
  --cluster eksworkshop-eksctl \
  --wait

aws iam delete-policy \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/k8s-asg-policy

export ASG_NAME=$(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].AutoScalingGroupName" --output text)

aws autoscaling \
  update-auto-scaling-group \
  --auto-scaling-group-name ${ASG_NAME} \
  --min-size 3 \
  --desired-capacity 3 \
  --max-size 3

kubectl delete hpa,svc php-apache

kubectl delete deployment php-apache

kubectl delete pod load-generator

cd ~/environment

rm -rf ~/environment/cluster-autoscaler

kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.1/components.yaml

kubectl delete ns metrics

helm uninstall kube-ops-view

unset ASG_NAME
unset AUTOSCALER_VERSION
unset K8S_VERSION
```

# Introduction to RBAC
In this module, we’ll learn about how role based access control (RBAC) works in kubernetes.

## What is RBAC?
According to [the official kubernetes docs](https://kubernetes.io/docs/reference/access-authn-authz/rbac/):

_Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise._

The core logical components of RBAC are:

- Entity
A group, user, or service account (an identity representing an application that wants to execute certain operations (actions) and requires permissions to do so).

- Resource
A pod, service, or secret that the entity wants to access using the certain operations.

- Role
Used to define rules for the actions the entity can take on various resources.

- Role binding
This attaches (binds) a role to an entity, stating that the set of rules define the actions permitted by the attached entity on the specified resources.

There are two types of Roles (Role, ClusterRole) and the respective bindings (RoleBinding, ClusterRoleBinding). These differentiate between authorization in a namespace or cluster-wide.

- Namespace

Namespaces are an excellent way of creating security boundaries, they also provide a unique scope for object names as the ‘namespace’ name implies. They are intended to be used in multi-tenant environments to create virtual kubernetes clusters on the same physical cluster.

### Objectives for this module
In this module, we’re going to explore k8s RBAC by creating an IAM user called rbac-user who is authenticated to access the EKS cluster but is only authorized (via RBAC) to list, get, and watch pods and deployments in the ‘rbac-test’ namespace.

To achieve this, we’ll create an IAM user, map that user to a kubernetes role, then perform kubernetes actions under that user’s context.


## Install the test Pods
In this tutorial, we’re going to demonstrate how to provide limited access to pods running in the rbac-test namespace for a user named rbac-user.

To do that, let’s first create the rbac-test namespace, and then install nginx into it:
```
kubectl create namespace rbac-test
kubectl create deploy nginx --image=nginx -n rbac-test
```

To verify the test pods were properly installed, run:
```
kubectl get all -n rbac-test
```

Output should be similar to:

```
NAME                       READY   STATUS    RESTARTS   AGE
pod/nginx-5c7588df-8mvxx   1/1     Running   0          48s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           48s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-5c7588df   1         1         1       48s

```

## Create a user
__For the sake of simplicity, in this module, we will save credentials to a file to make it easy to toggle back and forth between users. Never do this in production or with credentials that have privileged access; It is not a security best practice to store credentials on the filesystem.__

From within the Cloud9 terminal, create a new user called rbac-user, and generate/save credentials for it:
```
aws iam create-user --user-name rbac-user
aws iam create-access-key --user-name rbac-user | tee /tmp/create_output.json
```

By running the previous step, you should get a response similar to:

```
{
    "AccessKey": {
        "UserName": "rbac-user",
        "Status": "Active",
        "CreateDate": "2019-07-17T15:37:27Z",
        "SecretAccessKey": < AWS Secret Access Key > ,
        "AccessKeyId": < AWS Access Key >
    }
}
```

To make it easy to switch back and forth between the admin user you created the cluster with, and this new rbac-user, run the following command to create a script that when sourced, sets the active user to be rbac-user:
```
cat << EoF > rbacuser_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq -r .AccessKey.SecretAccessKey /tmp/create_output.json)
export AWS_ACCESS_KEY_ID=$(jq -r .AccessKey.AccessKeyId /tmp/create_output.json)
EoF
```

## Map an IAM User to K8s
Next, we’ll define a k8s user called rbac-user, and map to its IAM user counterpart. Run the following to get the existing ConfigMap and save into a file called aws-auth.yaml:
```
kubectl get configmap -n kube-system aws-auth -o yaml | grep -v "creationTimestamp\|resourceVersion\|selfLink\|uid" | sed '/^  annotations:/,+2 d' > aws-auth.yaml
```

Next append the rbac-user mapping to the existing configMap

```
cat << EoF >> aws-auth.yaml
data:
  mapUsers: |
    - userarn: arn:aws:iam::${ACCOUNT_ID}:user/rbac-user
      username: rbac-user
EoF
```

Some of the values may be dynamically populated when the file is created. To verify everything populated and was created correctly, run the following:
```
cat aws-auth.yaml
```

And the output should reflect that rolearn and userarn populated, similar to:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: arn:aws:iam::123456789:user/rbac-user
      username: rbac-user
```

Next, apply the ConfigMap to apply this mapping to the system:

```
kubectl apply -f aws-auth.yaml
```

## Test the new user
Up until now, as the cluster operator, you’ve been accessing the cluster as the admin user. Let’s now see what happens when we access the cluster as the newly created rbac-user.

Issue the following command to source the rbac-user’s AWS IAM user environmental variables:

```
. rbacuser_creds.sh
```

By running the above command, you’ve now set AWS environmental variables which should override the default admin user or role. To verify we’ve overrode the default user settings, run the following command:
```
aws sts get-caller-identity
```
You should see something similar to below, where we’re now making API calls as rbac-user:

```

{
    "Account": <AWS Account ID>,
    "UserId": <AWS User ID>,
    "Arn": "arn:aws:iam::<AWS Account ID>:user/rbac-user"
}
```

Now that we’re making calls in the context of the rbac-user, lets quickly make a request to get all pods:
```
kubectl get pods -n rbac-test
```
You should get a response back similar to:


```
No resources found.  Error from server (Forbidden): pods is forbidden: User "rbac-user" cannot list resource "pods" in API group "" in the namespace "rbac-test"
```

We already created the `rbac-user`, so why did we get that error?

Just creating the user doesn’t give that user access to any resources in the cluster. In order to achieve that, we’ll need to define a role, and then bind the user to that role. We’ll do that next.

## Create the role and the role binding
As mentioned earlier, we have our new user rbac-user, but its not yet bound to any roles. In order to do that, we’ll need to switch back to our default admin user.

Run the following to unset the environmental variables that define us as rbac-user:
```
unset AWS_SECRET_ACCESS_KEY
unset AWS_ACCESS_KEY_ID
```

To verify we’re the admin user again, and no longer rbac-user, issue the following command:

```
aws sts get-caller-identity
```

The output should show the user is no longer rbac-user:

```
{
    "Account": <AWS Account ID>,
    "UserId": <AWS User ID>,
    "Arn": "arn:aws:iam::<your AWS account ID>:assumed-role/eksworkshop-admin/i-123456789"
}
```

Now that we’re the admin user again, we’ll create a role called pod-reader that provides list, get, and watch access for pods and deployments, but only for the rbac-test namespace. Run the following to create this role:
```
cat << EoF > rbacuser-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: rbac-test
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["list","get","watch"]
- apiGroups: ["extensions","apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]
EoF
We have the user, we have the role, and now we’re bind them together with a RoleBinding resource. Run the following to create this RoleBinding:

cat << EoF > rbacuser-role-binding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: rbac-test
subjects:
- kind: User
  name: rbac-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EoF
```

Next, we apply the Role, and RoleBindings we created:
```
kubectl apply -f rbacuser-role.yaml
kubectl apply -f rbacuser-role-binding.yaml
```

## Verify the role and binding
Now that the user, Role, and RoleBinding are defined, lets switch back to rbac-user, and test.

To switch back to rbac-user, issue the following command that sources the rbac-user env vars, and verifies they’ve taken:
```
. rbacuser_creds.sh; aws sts get-caller-identity
```

You should see output reflecting that you are logged in as rbac-user.

As rbac-user, issue the following to get pods in the rbac namespace:
```
kubectl get pods -n rbac-test
```
The output should be similar to:

```
NAME                    READY     STATUS    RESTARTS   AGE
nginx-55bd7c9fd-kmbkf   1/1       Running   0          23h
```

Try running the same command again, but outside of the rbac-test namespace:
```
kubectl get pods -n kube-system
```

You should get an error similar to:

```
No resources found.
Error from server (Forbidden): pods is forbidden: User "rbac-user" cannot list resource "pods" in API group "" in the namespace "kube-system"
```

Because the role you are bound to does not give you access to any namespace other than rbac-test.

## cleanup
Once you have completed this module, you can cleanup the files and resources you created by issuing the following commands:
```
unset AWS_SECRET_ACCESS_KEY
unset AWS_ACCESS_KEY_ID
kubectl delete namespace rbac-test
rm rbacuser_creds.sh
rm rbacuser-role.yaml
rm rbacuser-role-binding.yaml
aws iam delete-access-key --user-name=rbac-user --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/create_output.json)
aws iam delete-user --user-name rbac-user
rm /tmp/create_output.json
```

Next remove the rbac-user mapping from the existing configMap by editing the existing aws-auth.yaml file:
```
data:
  mapUsers: |
    []
```

And apply the ConfigMap and delete the aws-auth.yaml file

```
kubectl apply -f aws-auth.yaml
rm aws-auth.yaml
```

# Using IAM groups to manage Kubernetes cluster access
In this module, we’ll learn about how to simplify access to different parts of the kubernetes clusters depending on IAM Groups

## Kubernetes authenticationIn the intro to RBAC module, we have seen how we can give access to individual users to Kubernetes.

If you have different teams which needs different kind of cluster access, it would be difficult to manually add or remove access for each EKS clusters you want them to give or remove access from.

We can leverage on AWS IAM Groups to easily add or remove users and give them permission to whole cluster, or just part of it depending on which groups they belong to.

In this lesson, we will create 3 IAM roles that we will map to 3 IAM groups.

## Create IAM Roles
We are going to create 3 roles:

- a k8sAdmin role which will have admin rights in our EKS cluster
- a k8sDev role which will give access to the developers namespace in our EKS cluster
- a k8sInteg role which will give access to the integration namespace in our EKS cluster

Create the roles:
```
POLICY=$(echo -n '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"AWS":"arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':root"},"Action":"sts:AssumeRole","Condition":{}}]}')

echo ACCOUNT_ID=$ACCOUNT_ID
echo POLICY=$POLICY

aws iam create-role \
  --role-name k8sAdmin \
  --description "Kubernetes administrator role (for AWS IAM Authenticator for Kubernetes)." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

aws iam create-role \
  --role-name k8sDev \
  --description "Kubernetes developer role (for AWS IAM Authenticator for Kubernetes)." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

aws iam create-role \
  --role-name k8sInteg \
  --description "Kubernetes role for integration namespace in quick cluster." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

```

__In this example, the `assume-role-policy` allows the root account to assume the role. We are going to allow specific groups to also be able to assume those roles. Check [the official documentation](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts-technical-overview.html) for more information.__

Because the above roles are only used to authenticate within our EKS cluster, they don’t need to have AWS permissions. We will only use them to allow some IAM groups to assume this role in order to have access to our EKS cluster.


## Create IAM Groups
We want to have different IAM users which will be added to specific IAM groups in order to have different rights in the kubernetes cluster.

We will define 3 groups:

- k8sAdmin - users from this group will have admin rights on the kubernetes cluster
- k8sDev - users from this group will have full access only in the development namespace of the cluster
- k8sInteg - users from this group will have access to integration namespace.

In fact, users from `k8sDev` and `k8sInteg` groups will only have access to namespaces where we will define kubernetes RBAC access for their associated kubernetes role. We’ll see this but first, let’s creates the groups.

### Create k8sAdmin IAM Group
The k8sAdmin Group will be allowed to assume the k8sAdmin IAM Role.
```
aws iam create-group --group-name k8sAdmin
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sAdmin Role:
```
ADMIN_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sAdmin"
    }
  ]
}')
echo ADMIN_GROUP_POLICY=$ADMIN_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sAdmin \
--policy-name k8sAdmin-policy \
--policy-document "$ADMIN_GROUP_POLICY"

```

### Create k8sDev IAM Group
The k8sDev Group will be allowed to assume the k8sDev IAM Role.

```
aws iam create-group --group-name k8sDev
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sDev Role:
```
DEV_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sDev"
    }
  ]
}')
echo DEV_GROUP_POLICY=$DEV_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sDev \
--policy-name k8sDev-policy \
--policy-document "$DEV_GROUP_POLICY"
```

### Create k8sInteg IAM Group
```
aws iam create-group --group-name k8sInteg
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sInteg Role:

```
INTEG_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sInteg"
    }
  ]
}')
echo INTEG_GROUP_POLICY=$INTEG_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sInteg \
--policy-name k8sInteg-policy \
--policy-document "$INTEG_GROUP_POLICY"
```

You now should have your 3 groups
```
aws iam list-groups
```

```
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "k8sAdmin",
            "GroupId": "AGPAZRV3OHPJZGT2JKVDV",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sAdmin",
            "CreateDate": "2020-04-07T13:32:52Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sDev",
            "GroupId": "AGPAZRV3OHPJUOBR375KI",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sDev",
            "CreateDate": "2020-04-07T13:33:15Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sInteg",
            "GroupId": "AGPAZRV3OHPJR6GM6PFDG",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sInteg",
            "CreateDate": "2020-04-07T13:33:25Z"
        }
    ]
}
```

## Create IAM Users
In order to test our scenarios, we will create 3 users, one for each groups we created :
```
aws iam create-user --user-name PaulAdmin
aws iam create-user --user-name JeanDev
aws iam create-user --user-name PierreInteg
```

Add users to associated groups:

```
aws iam add-user-to-group --group-name k8sAdmin --user-name PaulAdmin
aws iam add-user-to-group --group-name k8sDev --user-name JeanDev
aws iam add-user-to-group --group-name k8sInteg --user-name PierreInteg
```

Check users are correctly added in their groups:

```
aws iam get-group --group-name k8sAdmin
aws iam get-group --group-name k8sDev
aws iam get-group --group-name k8sInteg
```

__For the sake of simplicity, in this module, we will save credentials to a file to make it easy to toggle back and forth between users. Never do this in production or with credentials that have priviledged access; It is not a security best practice to store credentials on the filesystem.__

Retrieve Access Keys for our fake users:
```
aws iam create-access-key --user-name PaulAdmin | tee /tmp/PaulAdmin.json
aws iam create-access-key --user-name JeanDev | tee /tmp/JeanDev.json
aws iam create-access-key --user-name PierreInteg | tee /tmp/PierreInteg.json
```

Recap:

- PaulAdmin is in the k8sAdmin group and will be able to assume the k8sAdmin role.
- JeanDev is in k8sDev Group and will be able to assume IAM role k8sDev
- PierreInteg is in k8sInteg group and will be able to assume IAM role k8sInteg

## Configure Kubernetes RBAC
You can refer to intro to RBAC module to understand the basic of Kubernetes RBAC.

### Create kubernetes namespaces
- development namespace will be accessible for IAM users from k8sDev group
- integration namespace will be accessible for IAM users from k8sInteg group
```
kubectl create namespace integration
kubectl create namespace development
```

### Configuring access to development namespace
We create a kubernetes role and rolebinding in the development namespace giving full access to the kubernetes user dev-user
```
cat << EOF | kubectl apply -f - -n development
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-role
rules:
  - apiGroups:
      - ""
      - "apps"
      - "batch"
      - "extensions"
    resources:
      - "configmaps"
      - "cronjobs"
      - "deployments"
      - "events"
      - "ingresses"
      - "jobs"
      - "pods"
      - "pods/attach"
      - "pods/exec"
      - "pods/log"
      - "pods/portforward"
      - "secrets"
      - "services"
    verbs:
      - "create"
      - "delete"
      - "describe"
      - "get"
      - "list"
      - "patch"
      - "update"
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-role-binding
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: dev-role
  apiGroup: rbac.authorization.k8s.io
EOF
```

The role we define will give full access to everything in that namespace. It is a Role, and not a ClusterRole, so it is going to be applied only in the development namespace.

_feel free to adapt or duplicate to any namespace you prefer._

### Configuring access to integration namespace

We create a kubernetes role and rolebinding in the integration namespace for full access with the kubernetes user integ-user
```
cat << EOF | kubectl apply -f - -n integration
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: integ-role
rules:
  - apiGroups:
      - ""
      - "apps"
      - "batch"
      - "extensions"
    resources:
      - "configmaps"
      - "cronjobs"
      - "deployments"
      - "events"
      - "ingresses"
      - "jobs"
      - "pods"
      - "pods/attach"
      - "pods/exec"
      - "pods/log"
      - "pods/portforward"
      - "secrets"
      - "services"
    verbs:
      - "create"
      - "delete"
      - "describe"
      - "get"
      - "list"
      - "patch"
      - "update"
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: integ-role-binding
subjects:
- kind: User
  name: integ-user
roleRef:
  kind: Role
  name: integ-role
  apiGroup: rbac.authorization.k8s.io
EOF
```

The role we define will give full access to everything in that namespace. It is a Role, and not a ClusterRole, so it is going to be applied only in the integration namespace.

## Configure Kubernetes Role Access
### Gives Access to our IAM Roles to EKS Cluster
In order to give access to the IAM Roles we defined previously to our EKS cluster, we need to add specific mapRoles to the aws-auth ConfigMap

The Advantage of using Role to access the cluster instead of specifying directly IAM users is that it will be easier to manage: we won’t have to update the ConfigMap each time we want to add or remove users, we will just need to add or remove users from the IAM Group and we just configure the ConfigMap to allow the IAM Role associated to the IAM Group.

Update the aws-auth ConfigMap to allow our IAM roles
The aws-auth ConfigMap from the kube-system namespace must be edited in order to allow or delete arn Groups.

This file makes the mapping between IAM role and k8S RBAC rights. We can edit it manually:

We can edit it using eksctl :
```
eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sDev \
  --username dev-user

eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg \
  --username integ-user

eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin \
  --username admin \
  --group system:masters
```

It can also be used to delete entries
```
eksctl delete iamidentitymapping --cluster eksworkshop-eksctlv --arn arn:aws:iam::xxxxxxxxxx:role/k8sDev --username dev-user
```

you should have the config map looking something like:
```
kubectl get cm -n kube-system aws-auth -o yaml
```
```
apiVersion: v1
data:
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::xxxxxxxxxx:role/eksctl-eksworkshop-eksctl-nodegro-NodeInstanceRole-14TKBWBD7KWFH
      username: system:node:{{EC2PrivateDNSName}}
    - rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sDev
      username: dev-user
    - rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sInteg
      username: integ-user
    - groups:
      - system:masters
      rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sAdmin
      username: admin
  mapUsers: |
    []
kind: ConfigMap
```

We can leverage eksctl to get a list of all identities managed in our cluster. Example:
```
eksctl get iamidentitymapping --cluster eksworkshop-eksctl
```


```
arn:aws:iam::xxxxxxxxxx:role/eksctl-quick-nodegroup-ng-fe1bbb6-NodeInstanceRole-1KRYARWGGHPTT	system:node:{{EC2PrivateDNSName}}	system:bootstrappers,system:nodes
arn:aws:iam::xxxxxxxxxx:role/k8sAdmin           admin					system:masters
arn:aws:iam::xxxxxxxxxx:role/k8sDev             dev-user
arn:aws:iam::xxxxxxxxxx:role/k8sInteg           integ-user
```

Here we have created:

- a RBAC role for K8sAdmin, that we map to admin user and give access to system:masters kubernetes Groups (so that it has Full Admin rights)
- a RBAC role for k8sDev that we map on dev-user in development namespace
- a RBAC role for k8sInteg that we map on integ-user in integration namespace

We will see on next section how we can test it.

## Test EKS Access
### Automate assumerole with aws cli
It is possible to automate the retrieval of temporary credentials for the assumed role by configuring the AWS CLI in the files `~/.aws/config` and `~/.aws/credentials`. As an example, we will define three profiles.

Add in ~/.aws/config:
```
mkdir -p ~/.aws

cat << EoF >> ~/.aws/config
[profile admin]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
source_profile=eksAdmin

[profile dev]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
source_profile=eksDev

[profile integ]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg
source_profile=eksInteg

EoF
```

Add in ~/.aws/credentials:
```
cat << EoF >> ~/.aws/credentials

[eksAdmin]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PaulAdmin.json)

[eksDev]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/JeanDev.json)

[eksInteg]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PierreInteg.json)

EoF
```

Test this with the dev profile:

```
aws sts get-caller-identity --profile dev

{
    "UserId": "AROAUD5VMKW75WJEHFU4X:botocore-session-1581687024",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sDev/botocore-session-1581687024"
}
```

The assumed-role is k8sDev, so we achieved our goal.

When specifying the __–profile dev__ parameter we automatically ask for temporary credentials for the role k8sDev. You can test this with `integ` and `admin` also.
```
aws sts get-caller-identity --profile admin

{
    "UserId": "AROAUD5VMKW77KXQAL7ZX:botocore-session-1582022121",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sAdmin/botocore-session-1582022121"
}
```

__When specifying the __–profile admin__ parameter we automatically ask for temporary credentials for the role `k8sAdmin`__

### Using AWS profiles with the Kubectl config file

It is also possible to specify the AWS_PROFILE to use with the aws-iam-authenticator in the `~/.kube/config` file, so that it will use the appropriate profile.

#### With dev profile
Create a new KUBECONFIG file to test this:
```
export KUBECONFIG=/tmp/kubeconfig-dev && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "dev"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-dev./g' | sponge $KUBECONFIG
```

__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__


We added the `--profile dev` parameter to our kubectl config file, so that this will ask `kubectl` to use our IAM role associated to our `dev` profile, and we rename the context using suffix `-dev`.

With this configuration we should be able to interact with the development namespace, because it has our RBAC role defined.

Let’s create a pod:

```
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx -n development
```

We can list the pods:
```

kubectl get pods -n development
```
```

NAME                     READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          28h
```

… but not in other namespaces:

```
kubectl get pods -n integration

```

```
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "integration"
```

#### Test with integ profile
```
export KUBECONFIG=/tmp/kubeconfig-integ && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "integ"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-integ./g' | sponge $KUBECONFIG
```
__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__

Let’s create a pod:
```
kubectl run --generator=run-pod/v1 nginx-integ --image=nginx -n integration
```

We can list the pods:

```
kubectl get pods -n integration
```

```
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s

```

… but not in other namespaces:
```
kubectl get pods -n development
```

```
Error from server (Forbidden): pods is forbidden: User "integ-user" cannot list resource "pods" in API group "" in the namespace "development"
```

#### Test with admin profile
```
export KUBECONFIG=/tmp/kubeconfig-admin && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "admin"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-admin./g' | sponge $KUBECONFIG
```

__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__


Let’s create a pod in the default namespace:

```
kubectl run --generator=run-pod/v1 nginx-admin --image=nginx
```

We can list the pods:

```
kubectl get pods
```

```
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s
```

We can list ALL pods in all namespaces:

```
kubectl get pods -A
```

```
NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
default       nginx-admin                1/1     Running   0          15s
development   nginx-dev                  1/1     Running   0          11m
integration   nginx-integ                1/1     Running   0          4m29s
kube-system   aws-node-mzbh4             1/1     Running   0          100m
kube-system   aws-node-p7nj7             1/1     Running   0          100m
kube-system   aws-node-v2kg9             1/1     Running   0          100m
kube-system   coredns-85bb8bb6bc-2qbx6   1/1     Running   0          105m
kube-system   coredns-85bb8bb6bc-87ndr   1/1     Running   0          105m
kube-system   kube-proxy-4n5lc           1/1     Running   0          100m
kube-system   kube-proxy-b65xm           1/1     Running   0          100m
kube-system   kube-proxy-pr7k7           1/1     Running   0          100m
```


###Switching between different contexts

It is possible to configure several Kubernetes API access keys in the same KUBECONFIG file, or just tell Kubectl to lookup several files:

```
export KUBECONFIG=/tmp/kubeconfig-dev:/tmp/kubeconfig-integ:/tmp/kubeconfig-admin
```

There is a tool [kubectx / kubens](https://github.com/ahmetb/kubectx) that will help manage KUBECONFIG files with several contexts:

```
curl -sSLO https://raw.githubusercontent.com/ahmetb/kubectx/master/kubectx && chmod 755 kubectx && sudo mv kubectx /usr/local/bin
```

I can use kubectx to quickly list or switch Kubernetes contexts:

```
kubectx

i-0397aa1339e238a99@eksworkshop-eksctl-admin.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-dev.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-integ.eu-west-2.eksctl.io
```

## Conclusion
In this module, we have seen how to configure EKS to provide finer access to users combining IAM Groups and Kubernetes RBAC. You can create different groups depending on your needs, configure their associated RBAC access in your cluster, and simply add or remove users from the group to grant or revoke access to your cluster.

Users will only have to configure their AWS CLI in order to automatically retrieve their associated rights in your cluster.

## cleanup
Once you have completed this module, you can cleanup the files and resources you created by issuing the following commands:

```
unset KUBECONFIG

kubectl delete namespace development integration
kubectl delete pod nginx-admin

eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg

aws iam remove-user-from-group --group-name k8sAdmin --user-name PaulAdmin
aws iam remove-user-from-group --group-name k8sDev --user-name JeanDev
aws iam remove-user-from-group --group-name k8sInteg --user-name PierreInteg

aws iam delete-group-policy --group-name k8sAdmin --policy-name k8sAdmin-policy
aws iam delete-group-policy --group-name k8sDev --policy-name k8sDev-policy
aws iam delete-group-policy --group-name k8sInteg --policy-name k8sInteg-policy

aws iam delete-group --group-name k8sAdmin
aws iam delete-group --group-name k8sDev
aws iam delete-group --group-name k8sInteg

aws iam delete-access-key --user-name PaulAdmin --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws iam delete-access-key --user-name JeanDev --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws iam delete-access-key --user-name PierreInteg --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)

aws iam delete-user --user-name PaulAdmin
aws iam delete-user --user-name JeanDev
aws iam delete-user --user-name PierreInteg

aws iam delete-role --role-name k8sAdmin
aws iam delete-role --role-name k8sDev
aws iam delete-role --role-name k8sInteg

rm /tmp/*.json
rm /tmp/kubeconfig*

# reset aws credentials and config files
rm  ~/.aws/{config,credentials}
aws configure set default.region ${AWS_REGION}
```

# IAM Roles for Service Accounts
In Kubernetes version 1.12, support was added for a new `ProjectedServiceAccountToken` feature, which is an OIDC JSON web token that also contains the service account identity, and supports a configurable audience.

Amazon EKS now hosts a public OIDC discovery endpoint per cluster containing the signing keys for the ProjectedServiceAccountToken JSON web tokens so external systems, like IAM, can validate and accept the Kubernetes-issued OIDC tokens.

OIDC federation access allows you to assume IAM roles via the Secure Token Service (STS), enabling authentication with an OIDC provider, receiving a JSON Web Token (JWT), which in turn can be used to assume an IAM role. Kubernetes, on the other hand, can issue so-called projected service account tokens, which happen to be valid OIDC JWTs for pods. Our setup equips each pod with a cryptographically-signed token that can be verified by STS against the OIDC provider of your choice to establish the pod’s identity.

New credential provider ”sts:AssumeRoleWithWebIdentity”

## Preparation
### Enabling IAM Roles for Service Accounts on your Cluster
The IAM roles for service accounts feature is available on new Amazon EKS Kubernetes version 1.16 or higher, and clusters that were updated to versions 1.14 or 1.13 on or after September 3rd, 2019.

__If your EKS cluster version is older than 1.16 your outputs may very. Please consider reading the [updating an Amazon EKS Cluster](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html) section in the User Guide.__
```
kubectl version --short
```

Output:

```
Client Version: v1.20.4-eks-6b7464
Server Version: v1.20.4-eks-6b7464
```

__If your aws cli version is lower than 1.19.122, use [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the User Guide__

```
aws --version
```

Output:

```
aws-cli/1.19.112 Python/2.7.18 Linux/4.14.232-177.418.amzn2.x86_64 botocore/1.20.112
```
### Retrieve OpenID Connect issuer URL
Your EKS cluster has an OpenID Connect issuer URL associated with it, and this will be used when configuring the IAM OIDC Provider. You can check it with:

aws eks describe-cluster --name eksworkshop-eksctl --query cluster.identity.oidc.issuer --output text
Output:


https://oidc.eks.us-east-1.amazonaws.com/id/09D1E682ADD23F8431B986E4B2E35BCB


## Create an OpenID Connect Provider
To use IAM roles for service accounts in your cluster, you must create an IAM OIDC Identity Provider. This can be done using the AWS Console, AWS CLIs and eksctl. For the sake of this workshop, we will use the last.

Check your eksctl version that your eksctl version is at least 0.57.0
```
eksctl version

0.57.0
```

### Create your IAM OIDC Identity Provider for your cluster
```
eksctl utils associate-iam-oidc-provider --cluster eksworkshop-eksctl --approve
```
```
2021-07-20 17:51:36 [ℹ]  eksctl version 0.57.0
2021-07-20 17:51:36 [ℹ]  using region us-east-1
2021-07-20 17:51:38 [ℹ]  will create IAM Open ID Connect provider for cluster "eksworkshop-eksctl" in "us-east-1"
2021-07-20 17:51:39 [✔]  created IAM Open ID Connect provider for cluster "eksworkshop-eksctl" in "us-east-1"
```

If you go to the Identity Providers in IAM Console, you will see OIDC provider has created for your cluster
![OIDC Provider](imgs/irsa-oidc.png "Cluster Creation Workflow")

## Creating an IAM role for service account
You will create an IAM policy that specifies the permissions that you would like the containers in your pods to have.

In this workshop we will use the AWS managed policy named __“AmazonS3ReadOnlyAccess”__ which allow get and list for all your S3 buckets.

Let’s start by finding the ARN for the __“AmazonS3ReadOnlyAccess”__ policy
```
aws iam list-policies --query 'Policies[?PolicyName==`AmazonS3ReadOnlyAccess`].Arn'
```
Output:
```
"arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
```

Now you will create a IAM role bound to a service account with read-only access to S3

```
eksctl create iamserviceaccount \
    --name iam-test \
    --namespace default \
    --cluster eksworkshop-eksctl \
    --attach-policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess \
    --approve \
    --override-existing-serviceaccounts
```

Output:
```
2021-07-21 10:31:14 [ℹ]  eksctl version 0.57.0
2021-07-21 10:31:14 [ℹ]  using region us-east-1
2021-07-21 10:31:17 [ℹ]  1 iamserviceaccount (default/iam-test) was included (based on the include/exclude rules)
2021-07-21 10:31:17 [!]  metadata of serviceaccounts that exist in Kubernetes will be updated, as --override-existing-serviceaccounts was set
2021-07-21 10:31:17 [ℹ]  1 task: { 2 sequential sub-tasks: { create IAM role for serviceaccount "default/iam-test", create serviceaccount "default/iam-test" } }
2021-07-21 10:31:17 [ℹ]  building iamserviceaccount stack "eksctl-eksworkshop-eksctl-addon-iamserviceaccount-default-iam-test"
2021-07-21 10:31:17 [ℹ]  deploying stack "eksctl-sandbox-addon-iamserviceaccount-default-iam-test"
2021-07-21 10:31:17 [ℹ]  waiting for CloudFormation stack "eksctl-eksworkshop-eksctl-addon-iamserviceaccount-default-iam-test"
2021-07-21 10:31:53 [ℹ]  created serviceaccount "default/iam-test"
```

__If you go to the [CloudFormation in IAM Console](https://console.aws.amazon.com/cloudformation/), you will find that the stack “eksctl-eksworkshop-eksctl-addon-iamserviceaccount-default-iam-test” has created a role for your service account.__


## Specifying an IAM role for Service Account
In the previous step, we created the IAM role that is associated with a service account named iam-test in the cluster.

First, let’s verify your service account iam-test exists
```
kubectl get sa iam-test
```

```
NAME       SECRETS   AGE
iam-test   1         5m
```

Make sure your service account with the ARN of the IAM role is annotated
```
kubectl describe sa iam-test
```

```

Name:                iam-test
Namespace:           default
Labels:              app.kubernetes.io/managed-by=eksctl
Annotations:         eks.amazonaws.com/role-arn: arn:aws:iam::40XXXXXXXX75:role/eksctl-sandbox-addon-iamserviceaccount-defau-Role1-1B37L4A1UEXYS
Image pull secrets:  <none>
Mountable secrets:   iam-test-token-zbk55
Tokens:              iam-test-token-zbk55
Events:              <none>
```

## Deploy Sample POD
Now that we have completed all the necessary configuration, we will run two kubernetes [jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/) with the newly created IAM role:

- job-s3.yaml: that will output the result of the command aws s3 ls (this job should be successful).
- job-ec2.yaml: that will output the result of the command aws ec2 describe-instances --region ${AWS_REGION} (this job should failed).

Before deploying the workloads, make sure to have the environment variables `AWS_REGION` and `ACCOUNT_ID` configured in your terminal prompt.

### List S3 buckets

Let’s start by testing if the service account can list the S3 buckets
```
mkdir ~/environment/irsa

cat <<EoF> ~/environment/irsa/job-s3.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: eks-iam-test-s3
spec:
  template:
    metadata:
      labels:
        app: eks-iam-test-s3
    spec:
      serviceAccountName: iam-test
      containers:
      - name: eks-iam-test
        image: amazon/aws-cli:latest
        args: ["s3", "ls"]
      restartPolicy: Never
EoF

kubectl apply -f ~/environment/irsa/job-s3.yaml
```

Make sure your job is completed.
```
kubectl get job -l app=eks-iam-test-s3
```

Output:

```
NAME              COMPLETIONS   DURATION   AGE
eks-iam-test-s3   1/1           2s         21m
```

Let’s check the logs to verify that the command ran successfully.
```
kubectl logs -l app=eks-iam-test-s3
```

Output:

```
2021-07-17 20:09:41 eksworkshop-eksctl-helm-charts
2021-07-18 19:22:37 eksworkshop-logs
```

__If the output lists some buckets, please move on to List EC2 Instances. If not, it is possible your account doesn’t have any s3 buckets. Please try to run theses extra commands.__

Let’s create an S3 bucket.
```
aws s3 mb s3://eksworkshop-$ACCOUNT_ID-$AWS_REGION --region $AWS_REGION
```

Output:

```
make_bucket: eksworkshop-40XXXXXXXX75-us-east-1
```

Now, let’s try that job again. But first, we should remove the old job.

```
kubectl delete job -l app=eks-iam-test-s3
```

Then we can re-create the job.
```
kubectl apply -f ~/environment/irsa/job-s3.yaml
```

Finally, we can have a look at the output.
```
kubectl logs -l app=eks-iam-test-s3
```

Output:

```
2021-07-21 14:06:24 eksworkshop-40XXXXXXXX75-us-east-1
```

### List EC2 Instances

Now Let’s confirm that the service account cannot list the EC2 instances
```
cat <<EoF> ~/environment/irsa/job-ec2.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: eks-iam-test-ec2
spec:
  template:
    metadata:
      labels:
        app: eks-iam-test-ec2
    spec:
      serviceAccountName: iam-test
      containers:
      - name: eks-iam-test
        image: amazon/aws-cli:latest
        args: ["ec2", "describe-instances", "--region", "${AWS_REGION}"]
      restartPolicy: Never
  backoffLimit: 0
EoF

kubectl apply -f ~/environment/irsa/job-ec2.yaml
```

Let’s verify the job status
```
kubectl get job -l app=eks-iam-test-ec2
```

Output:

```
NAME               COMPLETIONS   DURATION   AGE
eks-iam-test-ec2   0/1           39s        39s
```

It is normal that the job didn’t complete successfuly.

Finally we will review the logs
```
kubectl logs -l app=eks-iam-test-ec2
```

Output:

```
An error occurred (UnauthorizedOperation) when calling the DescribeInstances operation: You are not authorized to perform this operation.
```

## cleanup
To cleanup, follow these steps.
```
kubectl delete -f ~/environment/irsa/job-s3.yaml
kubectl delete -f ~/environment/irsa/job-ec2.yaml

eksctl delete iamserviceaccount \
    --name iam-test \
    --namespace default \
    --cluster eksworkshop-eksctl \
    --wait

rm -rf ~/environment/irsa/

aws s3 rb s3://eksworkshop-$ACCOUNT_ID-$AWS_REGION --region $AWS_REGION --force
```

# Security Groups for PODs
### Introduction
Containerized applications frequently require access to other services running within the cluster as well as external AWS services, such as Amazon Relational Database Service (Amazon RDS).

On AWS, controlling network level access between services is often accomplished via security groups.

Before the release of this new functionality, you could only assign security groups at the node level. And because all nodes inside a Node group share the security group, by attaching the security group to access the RDS instance to the Node group, all the pods running on theses nodes would have access the database even if only the green pod should have access.

![SG Per POD](imgs/sg-per-pod_1.png "Cluster Creation Workflow")

Security groups for pods integrate Amazon EC2 security groups with Kubernetes pods. You can use Amazon EC2 security groups to define rules that allow inbound and outbound network traffic to and from pods that you deploy to nodes running on many Amazon EC2 instance types. For a detailed explanation of this capability, see the Introducing security groups for pods blog post and the official documentation.

### Objectives
During this section of the workshop:

- We will create an Amazon RDS database protected by a security group called RDS_SG.
- We will create a security group called POD_SG that will be allowed to connect to the RDS instance.
- Then we will deploy a SecurityGroupPolicy that will automatically attach the POD_SG security group to a pod with the correct metadata.
- Finally we will deploy two pods (green and red) using the same image and verify that only one of them (green) can connect to the Amazon RDS database.


![SG Per POD](imgs/sg-per-pod_3.png "Cluster Creation Workflow")

## Prerequisites
__Security groups for pods are supported by most Nitro-based Amazon EC2 instance families, including the m5, c5, r5, p3, m6g, c6g, and r6g instance families. The t3 instance family is not supported and so we will create a second NodeGroup using one m5.large instance.__
```

mkdir ${HOME}/environment/sg-per-pod

cat << EoF > ${HOME}/environment/sg-per-pod/nodegroup-sec-group.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}

managedNodeGroups:
- name: nodegroup-sec-group
  desiredCapacity: 1
  instanceType: m5.large
EoF
```

```
eksctl create nodegroup -f ${HOME}/environment/sg-per-pod/nodegroup-sec-group.yaml
```

```
kubectl get nodes \
  --selector node.kubernetes.io/instance-type=m5.large

NAME                                           STATUS   ROLES    AGE     VERSION
ip-192-168-34-45.us-east-2.compute.internal    Ready    <none>   4m57s   v1.17.12-eks-7684af
```

## Security Group Creation
### Create and configure the security groups
First, let’s create the RDS security group (RDS_SG). It will be used by the Amazon RDS instance to control network access.
```
export VPC_ID=$(aws eks describe-cluster \
    --name eksworkshop-eksctl \
    --query "cluster.resourcesVpcConfig.vpcId" \
    --output text)

# create RDS security group
aws ec2 create-security-group \
    --description 'RDS SG' \
    --group-name 'RDS_SG' \
    --vpc-id ${VPC_ID}

# save the security group ID for future use
export RDS_SG=$(aws ec2 describe-security-groups \
    --filters Name=group-name,Values=RDS_SG Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" --output text)

echo "RDS security group ID: ${RDS_SG}"
```

Now, let’s create the pod security group (POD_SG).

```
# create the POD security group
aws ec2 create-security-group \
    --description 'POD SG' \
    --group-name 'POD_SG' \
    --vpc-id ${VPC_ID}

# save the security group ID for future use
export POD_SG=$(aws ec2 describe-security-groups \
    --filters Name=group-name,Values=POD_SG Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" --output text)

echo "POD security group ID: ${POD_SG}"
```

The pod needs to communicate with its node for DNS resolution, so we will update the Node Group security group accordingly.

```
export NODE_GROUP_SG=$(aws ec2 describe-security-groups \
    --filters Name=tag:Name,Values=eks-cluster-sg-eksworkshop-eksctl-* Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" \
    --output text)
echo "Node Group security group ID: ${NODE_GROUP_SG}"

# allow POD_SG to connect to NODE_GROUP_SG using TCP 53
aws ec2 authorize-security-group-ingress \
    --group-id ${NODE_GROUP_SG} \
    --protocol tcp \
    --port 53 \
    --source-group ${POD_SG}

# allow POD_SG to connect to NODE_GROUP_SG using UDP 53
aws ec2 authorize-security-group-ingress \
    --group-id ${NODE_GROUP_SG} \
    --protocol udp \
    --port 53 \
    --source-group ${POD_SG}
```

Finally, we will add two inbound traffic (ingress) rules to the RDS_SG security group:

- One for Cloud9 (to populate the database).
- One to allow POD_SG security group to connect to the database.

```
# Cloud9 IP
export C9_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)

# allow Cloud9 to connect to RDS
aws ec2 authorize-security-group-ingress \
    --group-id ${RDS_SG} \
    --protocol tcp \
    --port 5432 \
    --cidr ${C9_IP}/32

# Allow POD_SG to connect to the RDS
aws ec2 authorize-security-group-ingress \
    --group-id ${RDS_SG} \
    --protocol tcp \
    --port 5432 \
    --source-group ${POD_SG}

```

## RDS Creation
Now that our security groups are ready let’s create our Amazon RDS for PostgreSQL database.

We first need to create a [DB subnet groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html#USER_VPC.Subnets). We will use the same subnets as our EKS cluster.
```
export PUBLIC_SUBNETS_ID=$(aws ec2 describe-subnets \
    --filters "Name=vpc-id,Values=$VPC_ID" "Name=tag:Name,Values=eksctl-eksworkshop-eksctl-cluster/SubnetPublic*" \
    --query 'Subnets[*].SubnetId' \
    --output json | jq -c .)

# create a db subnet group
aws rds create-db-subnet-group \
    --db-subnet-group-name rds-eksworkshop \
    --db-subnet-group-description rds-eksworkshop \
    --subnet-ids ${PUBLIC_SUBNETS_ID}
```

We can now create our database.

```
# get RDS SG ID
export RDS_SG=$(aws ec2 describe-security-groups \
    --filters Name=group-name,Values=RDS_SG Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" --output text)

# generate a password for RDS
export RDS_PASSWORD="$(date | md5sum  |cut -f1 -d' ')"
echo ${RDS_PASSWORD}  > ~/environment/sg-per-pod/rds_password


# create RDS Postgresql instance
aws rds create-db-instance \
    --db-instance-identifier rds-eksworkshop \
    --db-name eksworkshop \
    --db-instance-class db.t3.micro \
    --engine postgres \
    --db-subnet-group-name rds-eksworkshop \
    --vpc-security-group-ids $RDS_SG \
    --master-username eksworkshop \
    --publicly-accessible \
    --master-user-password ${RDS_PASSWORD} \
    --backup-retention-period 0 \
    --allocated-storage 20

```

__It will take up to 4 minutes for the database to be created.__

You can verify if it’s available using this command.
```
aws rds describe-db-instances \
    --db-instance-identifier rds-eksworkshop \
    --query "DBInstances[].DBInstanceStatus" \
    --output text
```

Expected output

```
available
```

Now that the database is available, let’s get our database Endpoint.
```
# get RDS endpoint
export RDS_ENDPOINT=$(aws rds describe-db-instances \
    --db-instance-identifier rds-eksworkshop \
    --query 'DBInstances[0].Endpoint.Address' \
    --output text)

echo "RDS endpoint: ${RDS_ENDPOINT}"
```

Our last step is to create some content in the database.
```
sudo amazon-linux-extras install -y postgresql12

cd sg-per-pod

cat << EoF > ~/environment/sg-per-pod/pgsql.sql
CREATE TABLE welcome (column1 TEXT);
insert into welcome values ('--------------------------');
insert into welcome values ('Welcome to the EKS Training');
insert into welcome values ('--------------------------');
EoF

export RDS_PASSWORD=$(cat ~/environment/sg-per-pod/rds_password)

psql postgresql://eksworkshop:${RDS_PASSWORD}@${RDS_ENDPOINT}:5432/eksworkshop \
    -f ~/environment/sg-per-pod/pgsql.sql
```

### CNI Configuration
To enable this new functionality, Amazon EKS clusters have two new components running on the Kubernetes control plane:

- A mutating webhook responsible for adding limits and requests to pods requiring security groups.
- A resource controller responsible for managing [network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) associated with those pods.
To facilitate this feature, each worker node will be associated with a single trunk network interface, and multiple branch network interfaces. The trunk interface acts as a standard network interface attached to the instance. The VPC resource controller then associates branch interfaces to the trunk interface. This increases the number of network interfaces that can be attached per instance. Since security groups are specified with network interfaces, we are now able to schedule pods requiring specific security groups onto these additional network interfaces allocated to worker nodes.

First we need to attach a new IAM policy the Node group role to allow the EC2 instances to manage network interfaces, their private IP addresses, and their attachment and detachment to and from instances.

The following command adds the policy `AmazonEKSVPCResourceController` to a cluster role.
```
aws iam attach-role-policy \
    --policy-arn arn:aws:iam::aws:policy/AmazonEKSVPCResourceController \
    --role-name ${ROLE_NAME}
```

Next, we will enable the CNI plugin to manage network interfaces for pods by setting the ENABLE_POD_ENI variable to true in the aws-node DaemonSet.
```
kubectl -n kube-system set env daemonset aws-node ENABLE_POD_ENI=true


# let's way for the rolling update of the daemonset
kubectl -n kube-system rollout status ds aws-node
```

Once this setting is set to true, for each node in the cluster the plugin adds a label with the value vpc.amazonaws.com/has-trunk-attached=true to the compatible instances. The VPC resource controller creates and attaches one special network interface called a trunk network interface with the description aws-k8s-trunk-eni.
```
 kubectl get nodes \
  --selector  eks.amazonaws.com/nodegroup=nodegroup-sec-group \
  --show-labels
```

![CNI Configuration](imgs/sg-per-pod_4.png "Cluster Creation Workflow")

## SecurityGroup Policy
A new Custom Resource Definition (CRD) has also been added automatically at the cluster creation. Cluster administrators can specify which security groups to assign to pods through the SecurityGroupPolicy CRD. Within a namespace, you can select pods based on pod labels, or based on labels of the service account associated with a pod. For any matching pods, you also define the security group IDs to be applied.

You can verify the CRD is present with this command.
```
kubectl get crd securitygrouppolicies.vpcresources.k8s.aws
```

Output

```
securitygrouppolicies.vpcresources.k8s.aws   2020-11-04T17:01:27Z
```

The webhook watches SecurityGroupPolicy custom resources for any changes, and automatically injects matching pods with the extended resource request required for the pod to be scheduled onto a node with available branch network interface capacity. Once the pod is scheduled, the resource controller will create and attach a branch interface to the trunk interface. Upon successful attachment, the controller adds an annotation to the pod object with the branch interface details.

Now let’s create our policy.
```
cat << EoF > ~/environment/sg-per-pod/sg-policy.yaml
apiVersion: vpcresources.k8s.aws/v1beta1
kind: SecurityGroupPolicy
metadata:
  name: allow-rds-access
spec:
  podSelector:
    matchLabels:
      app: green-pod
  securityGroups:
    groupIds:
      - ${POD_SG}
EoF
```

As we can see, if the pod has the label app: green-pod, a security group will be attached to it.

We can finally deploy it in a specific namespace.
```
kubectl create namespace sg-per-pod

kubectl -n sg-per-pod apply -f ~/environment/sg-per-pod/sg-policy.yaml
```

```
kubectl -n sg-per-pod describe securitygrouppolicy
```

Output

```
Name:         allow-rds-access
Namespace:    sg-per-pod
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"vpcresources.k8s.aws/v1beta1","kind":"SecurityGroupPolicy","metadata":{"annotations":{},"name":"allow-rds-access","namespac...
API Version:  vpcresources.k8s.aws/v1beta1
Kind:         SecurityGroupPolicy
Metadata:
  Creation Timestamp:  2020-12-03T04:35:57Z
  Generation:          1
  Resource Version:    9142629
  Self Link:           /apis/vpcresources.k8s.aws/v1beta1/namespaces/sg-per-pod/securitygrouppolicies/allow-rds-access
  UID:                 bf1e329d-816e-4ab0-abe8-934cadabfdd3
Spec:
  Pod Selector:
    Match Labels:
      App:  green-pod
  Security Groups:
    Group Ids:
      sg-0ff967bc903e9639e
Events:  <none>
```

## PODs Deployment
### Kubernetes secrets
Before deploying our two pods we need to provide them with the RDS endpoint and password. We will create a kubernetes secret.
```
export RDS_PASSWORD=$(cat ~/environment/sg-per-pod/rds_password)

export RDS_ENDPOINT=$(aws rds describe-db-instances \
    --db-instance-identifier rds-eksworkshop \
    --query 'DBInstances[0].Endpoint.Address' \
    --output text)

kubectl create secret generic rds\
    --namespace=sg-per-pod \
    --from-literal="password=${RDS_PASSWORD}" \
    --from-literal="host=${RDS_ENDPOINT}"

kubectl -n sg-per-pod describe  secret rds
```

Output

```
Name:         rds
Namespace:    sg-per-pod
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
host:      56 bytes
password:  32 bytes

```

### Deployments
The required deployment files are in the YAMLs directory of this repository.


Take some time to explore both YAML files and see the different between the two.
![YAML Diff](imgs/sg-per-pod_5.png "Cluster Creation Workflow")

### Green Pod
Now let’s deploy the green pod
```
kubectl -n sg-per-pod apply -f ~/environment/sg-per-pod/green-pod.yaml

kubectl -n sg-per-pod rollout status deployment green-pod
```

The container will try to:

- Connect to the database and will output the content of a table to STDOUT.
- If the database connection failed, the error message will also be outputted to STDOUT.

Let’s verify the logs.
```
export GREEN_POD_NAME=$(kubectl -n sg-per-pod get pods -l app=green-pod -o jsonpath='{.items[].metadata.name}')

kubectl -n sg-per-pod  logs -f ${GREEN_POD_NAME}
```

Output

```
[('--------------------------',), ('Welcome to the EKS Training',), ('--------------------------',)]
[('--------------------------',), ('Welcome to the EKS Training',), ('--------------------------',)]
```

__use CTRL+C to exit the log__

As we can see, our attempt was successful!

Now let’s verify that:

- An ENI is attached to the pod.
- And the ENI has the security group POD_SG attached to it.
We can find the ENI ID in the pod Annotations section using this command.
```
kubectl -n sg-per-pod  describe pod $GREEN_POD_NAME | head -11
```

Output

```
Name:         green-pod-5c786d8dff-4kmvc
Namespace:    sg-per-pod
Priority:     0
Node:         ip-192-168-33-222.us-east-2.compute.internal/192.168.33.222
Start Time:   Thu, 03 Dec 2020 05:25:54 +0000
Labels:       app=green-pod
              pod-template-hash=5c786d8dff
Annotations:  kubernetes.io/psp: eks.privileged
              vpc.amazonaws.com/pod-eni:
                [{"eniId":"eni-0d8a3a3a7f2eb57ab","ifAddress":"06:20:0d:3c:5f:bc","privateIp":"192.168.47.64","vlanId":1,"subnetCidr":"192.168.32.0/19"}]
Status:       Running
```

You can verify that the security group POD_SG is attached to the eni shown above by opening [this](https://console.aws.amazon.com/ec2/home?#NIC:search=POD_SG) link.

![Network Interfaces](imgs/sg-per-pod_6.png "Cluster Creation Workflow")

### Red Pod
We will deploy the red pod and verify that it’s unable to connect to the database.

Just like for the green pod, the container will try to:

- Connect to the database and will output to STDOUT the content of a table.
- If the database connection failed, the error message will also be outputted to STDOUT.
```
kubectl -n sg-per-pod apply -f ~/environment/sg-per-pod/red-pod.yaml

kubectl -n sg-per-pod rollout status deployment red-pod
```

Let’s verify the logs (use CTRL+C to exit the log)
```
export RED_POD_NAME=$(kubectl -n sg-per-pod get pods -l app=red-pod -o jsonpath='{.items[].metadata.name}')

kubectl -n sg-per-pod  logs -f ${RED_POD_NAME}
```

Output

```
Database connection failed due to timeout expired
```

Finally let’s verify that the pod doesn’t have an eniId annotation.
```
kubectl -n sg-per-pod  describe pod ${RED_POD_NAME} | head -11
```

Output

```
Name:         red-pod-7f68d78475-vlm77
Namespace:    sg-per-pod
Priority:     0
Node:         ip-192-168-6-158.us-east-2.compute.internal/192.168.6.158
Start Time:   Thu, 03 Dec 2020 07:08:28 +0000
Labels:       app=red-pod
              pod-template-hash=7f68d78475
Annotations:  kubernetes.io/psp: eks.privileged
Status:       Running
IP:           192.168.0.188
IPs:
```

Conclusion
In this module, we configured our EKS cluster to enable the security groups per pod feature.

We created a SecurityGroup Policy and deployed 2 pods (using the same docker image) and a RDS Database protected by a Security Group.

Based on this policy, only one of the two pods was able to connect to the database.

Finally using the CLI and the AWS console, we were able to locate the pod’s ENI and verify that the Security Group was attached to it.

### cleanup
```

export VPC_ID=$(aws eks describe-cluster \
    --name eksworkshop-eksctl \
    --query "cluster.resourcesVpcConfig.vpcId" \
    --output text)
export RDS_SG=$(aws ec2 describe-security-groups \
    --filters Name=group-name,Values=RDS_SG Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" --output text)
export POD_SG=$(aws ec2 describe-security-groups \
    --filters Name=group-name,Values=POD_SG Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" --output text)
export C9_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)
export NODE_GROUP_SG=$(aws ec2 describe-security-groups \
    --filters Name=tag:Name,Values=eks-cluster-sg-eksworkshop-eksctl-* Name=vpc-id,Values=${VPC_ID} \
    --query "SecurityGroups[0].GroupId" \
    --output text)

# uninstall the RPM package
sudo yum remove -y $(sudo yum list installed | grep amzn2extra-postgresql12 | awk '{ print $1}')

# delete database
aws rds delete-db-instance \
    --db-instance-identifier rds-eksworkshop \
    --delete-automated-backups \
    --skip-final-snapshot

# delete kubernetes element
kubectl -n sg-per-pod delete -f ~/environment/sg-per-pod/green-pod.yaml
kubectl -n sg-per-pod delete -f ~/environment/sg-per-pod/red-pod.yaml
kubectl -n sg-per-pod delete -f ~/environment/sg-per-pod/sg-policy.yaml
kubectl -n sg-per-pod delete secret rds

# delete the namespace
kubectl delete ns sg-per-pod

# disable ENI trunking
kubectl -n kube-system set env daemonset aws-node ENABLE_POD_ENI=false
kubectl -n kube-system rollout status ds aws-node

# detach the IAM policy
aws iam detach-role-policy \
    --policy-arn arn:aws:iam::aws:policy/AmazonEKSVPCResourceController \
    --role-name ${ROLE_NAME}

# remove the security groups rules
aws ec2 revoke-security-group-ingress \
    --group-id ${RDS_SG} \
    --protocol tcp \
    --port 5432 \
    --source-group ${POD_SG}

aws ec2 revoke-security-group-ingress \
    --group-id ${RDS_SG} \
    --protocol tcp \
    --port 5432 \
    --cidr ${C9_IP}/32

aws ec2 revoke-security-group-ingress \
    --group-id ${NODE_GROUP_SG} \
    --protocol tcp \
    --port 53 \
    --source-group ${POD_SG}

aws ec2 revoke-security-group-ingress \
    --group-id ${NODE_GROUP_SG} \
    --protocol udp \
    --port 53 \
    --source-group ${POD_SG}

# delete POD security group
aws ec2 delete-security-group \
    --group-id ${POD_SG}
```

Verify the RDS instance has been deleted.
```
aws rds describe-db-instances \
    --db-instance-identifier rds-eksworkshop \
    --query "DBInstances[].DBInstanceStatus" \
    --output text
```

Expected output

```
An error occurred (DBInstanceNotFound) when calling the DescribeDBInstances operation: DBInstance rds-eksworkshop not found.
```

We can now safely delete the DB security group and the DB subnet group.

```
# delete RDS SG
aws ec2 delete-security-group \
    --group-id ${RDS_SG}

# delete DB subnet group
aws rds delete-db-subnet-group \
    --db-subnet-group-name rds-eksworkshop
```
Finally, we will delete the EKS Nodegroup
```
# delete the nodegroup
eksctl delete nodegroup -f ${HOME}/environment/sg-per-pod/nodegroup-sec-group.yaml --approve

# remove the trunk label
kubectl label node  --all 'vpc.amazonaws.com/has-trunk-attached'-

cd ~/environment
rm -rf sg-per-pod
```

# Securing cluster with network policies
In this module, we are going to use two tools to secure our cluster by using network policies and then integrating our cluster’s network policies with EKS security groups.

We will use Project Calico to enforce Kubernetes network policies in our cluster, protecting our various microservices.

![Project Calico](imgs/Project-Calico-logo-1000px.png "Cluster Creation Workflow")

## Create network policies using Calico
In this module, we will create some network policies using [Calico](https://www.projectcalico.org/) and see the rules in action.

Network policies allow you to define rules that determine what type of traffic is allowed to flow between different services. Using network policies you can also define rules to restrict traffic. They are a means to improve your cluster’s security.

For example, you can only allow traffic from frontend to backend in your application.

Network policies also help in isolating traffic within namespaces. For instance, if you have separate namespaces for development and production, you can prevent traffic flow between them by restrict pod to pod communication within the same namespace.

## Install Calico
Apply the Calico manifest from the [aws/amazon-vpc-cni-k8s](https://github.com/aws/amazon-vpc-cni-k8s) GitHub project. This creates the daemon sets in the kube-system namespace.
```
kubectl apply -f https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/master/config/v1.6/calico.yaml
```

Let’s go over few key features of the Calico manifest:

- We see an annotation throughout; [annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) are a way to attach `non-identifying` metadata to objects. This metadata is not used internally by Kubernetes, so they cannot be used to identify within k8s. Instead, they are used by external tools and libraries. Examples of annotations include build/release timestamps, client library information for debugging, or fields managed by a network policy like Calico in this case.
```
kind: DaemonSet
apiVersion: apps/v1
metadata:
  name: calico-node
  namespace: kube-system
  labels:
    k8s-app: calico-node
spec:
  selector:
    matchLabels:
      k8s-app: calico-node
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  template:
    metadata:
      labels:
        k8s-app: calico-node
      annotations:
        # This, along with the CriticalAddonsOnly toleration below,
        # marks the pod as a critical add-on, ensuring it gets
        # priority scheduling and that its resources are reserved
        # if it ever gets evicted.
        *scheduler**.alpha.kubernetes.io/critical-pod: ''*
        ...
```

In contrast, [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) in Kubernetes are intended to be used to specify identifying attributes for objects. They are used by selector queries or with label selectors. Since they are used internally by Kubernetes the structure of keys and values is constrained, to optimize queries.

- We see that the manifest has a tolerations attribute. [Taints and tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) work together to ensure pods are not scheduled onto inappropriate nodes. Taints are applied to nodes, and the only pods that can tolerate the taint are allowed to run on those nodes.
A taint consists of a key, a value for it and an effect, which can be:

- PreferNoSchedule: Prefer not to schedule intolerant pods to the tainted node
- NoSchedule: Do not schedule intolerant pods to the tainted node
- NoExecute: In addition to not scheduling, also evict intolerant pods that are already running on the node.

Like taints, tolerations also have a key value pair and an effect, with the addition of operator. Here in the Calico manifest, we see tolerations has just one attribute: Operator = exists. This means the key value pair is omitted and the toleration will match any taint, ensuring it runs on all nodes.

```
 tolerations:
      - operator: Exists
```

Watch the kube-system daemon sets and wait for the calico-node daemon set to have the DESIRED number of pods in the READY state.
```
kubectl get daemonset calico-node --namespace=kube-system
```

Expected Output:

```
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
calico-node   3         3         3       3            3           <none>          38s
```

## Stars policy demo
In this sub-module we create frontend, backend, client and UI services on the EKS cluster and define network policies to allow or block communication between these services. This demo also has a management UI that shows the available ingress and egress paths between each service.

### Create Resources
Before creating network polices, let’s create the required resources.

Create a new folder for the configuration files.
```
mkdir ~/environment/calico_resources
cd ~/environment/calico_resources
```

#### Stars Namespace
Copy/Paste the following commands into your Cloud9 Terminal.
```
cd ~/environment/calico_resources
```

Let’s examine our file by running cat namespace.yaml.

```
kind: Namespace
apiVersion: v1
metadata:
  name: stars
```

#### Create a namespace called stars:
The yaml manifest is in YAMLS directory of this repository.

```
kubectl apply -f namespace.yaml
```
We will create frontend and backend deployments and services in this namespace in later steps.

Copy/Paste the following commands into your Cloud9 Terminal.
```
cd ~/environment/calico_resources
cat management-ui.yaml
```
```
apiVersion: v1
kind: Namespace
metadata:
  name: management-ui
  labels:
    role: management-ui
---
apiVersion: v1
kind: Service
metadata:
  name: management-ui
  namespace: management-ui
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 9001
  selector:
    role: management-ui
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: management-ui
  namespace: management-ui
spec:
  replicas: 1
  selector:
    matchLabels:
      role: management-ui
  template:
    metadata:
      labels:
        role: management-ui
    spec:
      containers:
      - name: management-ui
        image: calico/star-collect:v0.1.0
        imagePullPolicy: Always
        ports:
        - containerPort: 9001
```

Create a management-ui namespace, with a management-ui service and deployment within that namespace:
```
kubectl apply -f management-ui.yaml
```

cat backend.yaml to see how the backend service is built:

```
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: stars
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    role: backend
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: stars
spec:
  replicas: 1
  selector:
    matchLabels:
      role: backend
  template:
    metadata:
      labels:
        role: backend
    spec:
      containers:
      - name: backend
        image: calico/star-probe:v0.1.0
        imagePullPolicy: Always
        command:
        - probe
        - --http-port=6379
        - --urls=http://frontend.stars:80/status,http://backend.stars:6379/status,http://client.client:9000/status
        ports:
        - containerPort: 6379
```

Let’s examine the frontend service with cat frontend.yaml:

```
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: stars
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    role: frontend
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: stars
spec:
  replicas: 1
  selector:
    matchLabels:
      role: frontend
  template:
    metadata:
      labels:
        role: frontend
    spec:
      containers:
      - name: frontend
        image: calico/star-probe:v0.1.0
        imagePullPolicy: Always
        command:
        - probe
        - --http-port=80
        - --urls=http://frontend.stars:80/status,http://backend.stars:6379/status,http://client.client:9000/status
        ports:
        - containerPort: 80
```

Create frontend and backend deployments and services within the stars namespace:
```
kubectl apply -f backend.yaml
kubectl apply -f frontend.yaml
```
Lastly, let’s examine how the client namespace, and a client service for a deployment are built. cat client.yaml:

```
kind: Namespace
apiVersion: v1
metadata:
  name: client
  labels:
    role: client
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: client
  namespace: client
spec:
  replicas: 1
  selector:
    matchLabels:
      role: client
  template:
    metadata:
      labels:
        role: client
    spec:
      containers:
      - name: client
        image: calico/star-probe:v0.1.0
        imagePullPolicy: Always
        command:
        - probe
        - --urls=http://frontend.stars:80/status,http://backend.stars:6379/status
        ports:
        - containerPort: 9000
---
apiVersion: v1
kind: Service
metadata:
  name: client
  namespace: client
spec:
  ports:
  - port: 9000
    targetPort: 9000
  selector:
    role: client
```
Apply the client configuraiton.

```
kubectl apply -f client.yaml
```

Check their status, and wait for all the pods to reach the Running status:
```
kubectl get pods --all-namespaces
```

Your output should look like this:

```
NAMESPACE       NAME                                                  READY   STATUS    RESTARTS   AGE
client          client-nkcfg                                          1/1     Running   0          24m
kube-system     aws-node-6kqmw                                        1/1     Running   0          50m
kube-system     aws-node-grstb                                        1/1     Running   1          50m
kube-system     aws-node-m7jg8                                        1/1     Running   1          50m
kube-system     calico-node-b5b7j                                     1/1     Running   0          28m
kube-system     calico-node-dw694                                     1/1     Running   0          28m
kube-system     calico-node-vtz9k                                     1/1     Running   0          28m
kube-system     calico-typha-75667d89cb-4q4zx                         1/1     Running   0          28m
kube-system     calico-typha-horizontal-autoscaler-78f747b679-kzzwq   1/1     Running   0          28m
kube-system     kube-dns-7cc87d595-bd9hq                              3/3     Running   0          1h
kube-system     kube-proxy-lp4vw                                      1/1     Running   0          50m
kube-system     kube-proxy-rfljb                                      1/1     Running   0          50m
kube-system     kube-proxy-wzlqg                                      1/1     Running   0          50m
management-ui   management-ui-wzvz4                                   1/1     Running   0          24m
stars           backend-tkjrx                                         1/1     Running   0          24m
stars           frontend-q4r84                                        1/1     Running   0          24m

```
It may take several minutes to download all the required Docker images.

To summarize the different resources we created:

- A namespace called stars
- frontend and backend deployments and services within stars namespace
- A namespace called management-ui
- Deployment and service management-ui for the user interface seen on the browser, in the management-ui namespace
- A namespace called client
- client deployment and service in client namespace

### Default Pod-to-pod communication
In Kubernetes, the pods by default can communicate with other pods, regardless of which host they land on. Every pod gets its own IP address so you do not need to explicitly create links between pods. This is demonstrated by the management-ui.

```
kind: Service
metadata:
  name: management-ui
  namespace: management-ui
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 9001
```
To open the Management UI, retrieve the DNS name of the Management UI using:
```
kubectl get svc -o wide -n management-ui
```

Copy the EXTERNAL-IP from the output, and paste into a browser. The EXTERNAL-IP column contains a value that ends with “elb.amazonaws.com” - the full value is the DNS address.

```
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP                                                              PORT(S)        AGE       SELECTOR
management-ui   LoadBalancer   10.100.239.7   a8b8c5f77eda911e8b1a60ea5d5305a4-720629306.us-east-1.elb.amazonaws.com   80:31919/TCP   9s        role=management-ui
```

The UI here shows the default behavior, of all services being able to reach each other.
![Pod to pod communication](imgs/calico-full-access.png "Cluster Creation Workflow")


### Apply network policies
In a production level cluster, it is not secure to have open pod to pod communication. Let’s see how we can isolate the services from each other.

Copy/Paste the following commands into your Cloud9 Terminal.
```
cd ~/environment/calico_resources
```

Let’s examine our file by running cat default-deny.yaml.

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: default-deny
spec:
  podSelector:
    matchLabels: {}
```

Let’s go over the network policy. Here we see the podSelector does not have any matchLabels, essentially blocking all the pods from accessing it.

Apply the network policy in the stars namespace (frontend and backend services) and the client namespace (client service):
```
kubectl apply -n stars -f default-deny.yaml
kubectl apply -n client -f default-deny.yaml
```

Upon refreshing your browser, you see that the management UI cannot reach any of the nodes, so nothing shows up in the UI.

Network policies in Kubernetes use labels to select pods, and define rules on what traffic is allowed to reach those pods. They may specify ingress or egress or both. Each rule allows traffic which matches both the from and ports sections.

Create two new network policies.

Copy/Paste the following commands into your Cloud9 Terminal.
```
cd ~/environment/calico_resources
```

Again, we can examine our file contents by running: cat allow-ui.yaml
```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: stars
  name: allow-ui
spec:
  podSelector:
    matchLabels: {}
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              role: management-ui
```
```
cat allow-ui-client.yaml
```
```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: client
  name: allow-ui
spec:
  podSelector:
    matchLabels: {}
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              role: management-ui
```
Challenge:
- How do we apply our network policies to allow the traffic we want?

Solution:
```
kubectl apply -f allow-ui.yaml
kubectl apply -f allow-ui-client.yaml
```

Upon refreshing your browser, you can see that the management UI can reach all the services, but they cannot communicate with each other.

![Calico Mgmt UI](imgs/calico-mgmtui-access.png "Cluster Creation Workflow")


### Allow Directional Traffic
Let’s see how we can allow directional traffic from client to frontend, and from frontend to backend.

Let’s examine this backend policy with cat backend-policy.yaml:

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: stars
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
    - from:
        - <EDIT: UPDATE WITH THE CONFIGURATION NEEDED TO WHITELIST FRONTEND USING PODSELECTOR>
      ports:
        - protocol: TCP
          port: 6379
```

Challenge:
After reviewing the manifest, you’ll see we have intentionally left few of the configuration fields for you to EDIT. Please edit the configuration as suggested. You can find helpful info in this [Kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

Solution:
```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: stars
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
```
Let’s examine the frontend policy with cat frontend-policy.yaml:

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: stars
  name: frontend-policy
spec:
  podSelector:
    matchLabels:
      role: frontend
  ingress:
    - from:
        - <EDIT: UPDATE WITH THE CONFIGURATION NEEDED TO WHITELIST CLIENT USING NAMESPACESELECTOR>
      ports:
        - protocol: TCP
          port: 80

```
Challenge:
Please edit the configuration as suggested. You can find helpful info in this [Kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
Solution:
```      
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: stars
  name: frontend-policy
spec:
  podSelector:
    matchLabels:
      role: frontend
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              role: client
      ports:
        - protocol: TCP
          port: 80
```
To allow traffic from frontend service to the backend service apply the following manifest:

```
kubectl apply -f backend-policy.yaml
```
And allow traffic from the client namespace to the frontend service:
```
kubectl apply -f frontend-policy.yaml
```
Upon refreshing your browser, you should be able to see the network policies in action:

![Cluster Creation Workflow](imgs/calico-client-f-b-access.png "Cluster Creation Workflow")
Let’s have a look at the backend-policy. Its spec has a podSelector that selects all pods with the label role:backend, and allows ingress from all pods that have the label role:frontend and on TCP port 6379, but not the other way round. Traffic is allowed in one direction on a specific port number.

```
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
```
The frontend-policy is similar, except it allows ingress from namespaces that have the label role: client on TCP port 80.

```
spec:
  podSelector:
    matchLabels:
      role: frontend
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              role: client
      ports:
        - protocol: TCP
          port: 80

```

### cleanup

cleanup the demo by deleting the namespaces:
```
kubectl delete namespace client stars management-ui
```

# Deploying Microservices to EKS Fargate
[AWS Fargate](https://docs.aws.amazon.com/eks/latest/userguide/fargate.html) is a technology that provides on-demand, right-sized compute capacity for containers. With AWS Fargate, you no longer have to provision, configure, or scale groups of virtual machines to run containers. This removes the need to choose server types, decide when to scale your node groups, or optimize cluster packing. You can control which pods start on Fargate and how they run with [Fargate profiles](https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html), which are defined as part of your Amazon EKS cluster.

In this module, we will deploy the game [2048 game](http://play2048.co/) on EKS Fargate and expose it to the Internet using an Application Load balancer.

## Creating a fargate profile
The Fargate profile allows an administrator to declare which pods run on Fargate. Each profile can have up to five selectors that contain a namespace and optional labels. You must define a namespace for every selector. The label field consists of multiple optional key-value pairs. Pods that match a selector (by matching a namespace for the selector and all of the labels specified in the selector) are scheduled on Fargate.

It is generally a good practice to deploy user application workloads into namespaces other than kube-system or default so that you have more fine-grained capabilities to manage the interaction between your pods deployed on to EKS. You will now create a new Fargate profile named applications that targets all pods destined for the fargate namespace.

### Create a Fargate profile
```
eksctl create fargateprofile \
  --cluster eksworkshop-eksctl \
  --name game-2048 \
  --namespace game-2048
```

Fargate profiles are immutable. However, you can create a new updated profile to replace an existing profile and then delete the original after the updated profile has finished creating

When your EKS cluster schedules pods on Fargate, the pods will need to make calls to AWS APIs on your behalf to do things like pull container images from Amazon ECR. The Fargate Pod Execution Role provides the IAM permissions to do this. This IAM role is automatically created for you by the above command.

Creation of a Fargate profile can take up to several minutes. Execute the following command after the profile creation is completed and you should see output similar to what is shown below.
```
eksctl get fargateprofile \
  --cluster eksworkshop-eksctl \
  -o yaml
```
Output:

```
- name: game-2048
  podExecutionRoleARN: arn:aws:iam::197520326489:role/eksctl-eksworkshop-eksctl-FargatePodExecutionRole-1NOQE05JKQEED
  selectors:
  - namespace: game-2048
  subnets:
  - subnet-02783ce3799e77b0b
  - subnet-0aa755ffdf08aa58f
  - subnet-0c6a156cf3d523597
```

Notice that the profile includes the private subnets in your EKS cluster. Pods running on Fargate are not assigned public IP addresses, so only private subnets (with no direct route to an Internet Gateway) are supported when you create a Fargate profile. Hence, while provisioning an EKS cluster, you must make sure that the VPC that you create contains one or more private subnets. When you create an EKS cluster with eksctl utility, under the hoods it creates a VPC that meets these requirements.

## Setting up the LB Controller
### AWS Load Balancer Controller
The AWS ALB Ingress Controller has been rebranded to [AWS Load Balancer Controller](https://github.com/kubernetes-sigs/aws-load-balancer-controller).

“AWS Load Balancer Controller” is a controller to help manage Elastic Load Balancers for a Kubernetes cluster.

- It satisfies Kubernetes Ingress resources by provisioning Application Load Balancers.
- It satisfies Kubernetes Service resources by provisioning Network Load Balancers.

### Helm
We will use Helm to install the ALB Ingress Controller.

Check to see if helm is installed:
```
helm version
```

If Helm is not found, see installing helm for instructions.

### Create IAM OIDC provider
First, we will have to set up an OIDC provider with the cluster.

This step is required to give IAM permissions to a Fargate pod running in the cluster using the IAM for Service Accounts feature.

Learn more about IAM Roles for Service Accounts in the Amazon EKS documentation.
```
eksctl utils associate-iam-oidc-provider \
    --region ${AWS_REGION} \
    --cluster eksworkshop-eksctl \
    --approve
```

### Create an IAM policy
The next step is to create the IAM policy that will be used by the AWS Load Balancer Controller.

This policy will be later associated to the Kubernetes Service Account and will allow the controller pods to create and manage the ELB’s resources in your AWS account for you.
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

### Create a IAM role and ServiceAccount for the Load Balancer controller
Next, create a Kubernetes Service Account by executing the following command
```
eksctl create iamserviceaccount \
  --cluster eksworkshop-eksctl \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve
```
The above command deploys a CloudFormation template that creates an IAM role and attaches the IAM policy to it.

The IAM role gets associated with a Kubernetes Service Account. You can see details of the service account created with the following command.
```
kubectl get sa aws-load-balancer-controller -n kube-system -o yaml
```
Output

```
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<AWS_ACCOUNT_ID>:role/eksctl-eksworkshop-eksctl-addon-iamserviceac-Role1-1MMJRJ4LWWHD8
  creationTimestamp: "2020-12-04T19:31:57Z"
  name: aws-load-balancer-controller
  namespace: kube-system
  resourceVersion: "3094"
  selfLink: /api/v1/namespaces/kube-system/serviceaccounts/aws-load-balancer-controller
  uid: aa940b27-796e-4cda-bbba-fe6ca8207c00
secrets:
- name: aws-load-balancer-controller-token-8pnww
```
For more information on IAM Roles for Service Accounts follow this link.

### Install the TargetGroupBinding CRDs
```
kubectl apply -k github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master
```

### Deploy the Helm chart from the Amazon EKS charts repo
Fist, We will verify if the AWS Load Balancer Controller version has beed set
```
if [ ! -x ${LBC_VERSION} ]
  then
    tput setaf 2; echo '${LBC_VERSION} has been set.'
  else
    tput setaf 1;echo '${LBC_VERSION} has NOT been set.'
fi
```

If the result is ${LBC_VERSION} has NOT been set., click here for the instructions.
```
helm repo add eks https://aws.github.io/eks-charts

export VPC_ID=$(aws eks describe-cluster \
                --name eksworkshop-eksctl \
                --query "cluster.resourcesVpcConfig.vpcId" \
                --output text)

helm upgrade -i aws-load-balancer-controller \
    eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=eksworkshop-eksctl \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set image.tag="${LBC_VERSION}" \
    --set region=${AWS_REGION} \
    --set vpcId=${VPC_ID}
```
You can check if the deployment has completed
```
kubectl -n kube-system rollout status deployment aws-load-balancer-controller
```

## Deploying Pods to Fargate

### Deploy the sample application
Deploy the game 2048 as a sample application to verify that the AWS Load Balancer Controller creates an Application Load Balancer as a result of the Ingress object.
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/examples/2048/2048_full.yaml
```

You can check if the deployment has completed
```
kubectl -n game-2048 rollout status deployment deployment-2048
```

Output:

```
Waiting for deployment "deployment-2048" rollout to finish: 0 of 5 updated replicas are available...
Waiting for deployment "deployment-2048" rollout to finish: 1 of 5 updated replicas are available...
Waiting for deployment "deployment-2048" rollout to finish: 2 of 5 updated replicas are available...
Waiting for deployment "deployment-2048" rollout to finish: 3 of 5 updated replicas are available...
Waiting for deployment "deployment-2048" rollout to finish: 4 of 5 updated replicas are available...
deployment "deployment-2048" successfully rolled out
```

Next, run the following command to list all the nodes in the EKS cluster and you should see output as follows:

```
kubectl get nodes
```
Output:

```
NAME                                                    STATUS   ROLES    AGE   VERSION
fargate-ip-192-168-110-35.us-east-2.compute.internal    Ready    <none>   47s   v1.17.9-eks-a84824
fargate-ip-192-168-142-4.us-east-2.compute.internal     Ready    <none>   47s   v1.17.9-eks-a84824
fargate-ip-192-168-169-29.us-east-2.compute.internal    Ready    <none>   55s   v1.17.9-eks-a84824
fargate-ip-192-168-174-79.us-east-2.compute.internal    Ready    <none>   39s   v1.17.9-eks-a84824
fargate-ip-192-168-179-197.us-east-2.compute.internal   Ready    <none>   50s   v1.17.9-eks-a84824
ip-192-168-20-197.us-east-2.compute.internal            Ready    <none>   16h   v1.17.11-eks-cfdc40
ip-192-168-33-161.us-east-2.compute.internal            Ready    <none>   16h   v1.17.11-eks-cfdc40
ip-192-168-68-228.us-east-2.compute.internal            Ready    <none>   16h   v1.17.11-eks-cfdc40
```

If your cluster has any worker nodes, they will be listed with a name starting wit the ip- prefix.

In addition to the worker nodes, if any, there will now be five additional fargate- nodes listed. These are merely kubelets from the microVMs in which your sample app pods are running under Fargate, posing as nodes to the EKS Control Plane. This is how the EKS Control Plane stays aware of the Fargate infrastructure under which the pods it orchestrates are running. There will be a “fargate” node added to the cluster for each pod deployed on Fargate.

## Ingress
### Ingress
After few seconds, verify that the Ingress resource is enabled:
```
kubectl get ingress/ingress-2048 -n game-2048
```
You should be able to see the following output

```
NAME           HOSTS   ADDRESS                                                                   PORTS   AGE
ingress-2048   *       k8s-game2048-ingress2-8ae3738fd5-1566954439.us-east-2.elb.amazonaws.com   80      14m
```
It could take 2 or 3 minutes for the ALB to be ready.

From your AWS Management Console, if you navigate to the EC2 dashboard and the select Load Balancers from the menu on the left-pane, you should see the details of the ALB instance similar to the following.

![Cluster Creation Workflow](imgs/LoadBalancer.png "Cluster Creation Workflow")

From the left-pane, if you select Target Groups and look at the registered targets under the Targets tab, you will see the IP addresses and ports of the sample app pods listed.

![Cluster Creation Workflow](imgs/LoadBalancerTargets.png "Cluster Creation Workflow")

Notice that the pods have been directly registered with the load balancer whereas when we worked with worker nodes in an earlier lab, the IP address of the worker nodes and the NodePort were registered as targets. The latter case is the Instance Mode where Ingress traffic starts at the ALB and reaches the Kubernetes worker nodes through each service’s NodePort and subsequently reaches the pods through the service’s ClusterIP. While running under Fargate, ALB operates in IP Mode, where Ingress traffic starts at the ALB and reaches the Kubernetes pods directly.

Illustration of request routing from an AWS Application Load Balancer to Pods on worker nodes in Instance mode:

![Cluster Creation Workflow](imgs/InstanceMode.png "Cluster Creation Workflow")

Illustration of request routing from an AWS Application Load Balancer to Fargate Pods in IP mode:

![Cluster Creation Workflow](imgs/IPMode.png "Cluster Creation Workflow")

At this point, your deployment is complete and you should be able to reach the game-2048 service from a browser using the DNS name of the ALB. You may get the DNS name of the load balancer either from the AWS Management Console or from the output of the following command.
```
export FARGATE_GAME_2048=$(kubectl get ingress/ingress-2048 -n game-2048 -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

echo "http://${FARGATE_GAME_2048}"
```

Output should look like this

```
http://3e100955-2048game-2048ingr-6fa0-1056911976.us-east-2.elb.amazonaws.com
```
![Cluster Creation Workflow](imgs/Browser.svg "Cluster Creation Workflow")

## cleanup
To delete the resources used in this module:
```

kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/examples/2048/2048_full.yaml

helm uninstall aws-load-balancer-controller \
    -n kube-system

eksctl delete iamserviceaccount \
    --cluster eksworkshop-eksctl \
    --name aws-load-balancer-controller \
    --namespace kube-system \
    --wait

aws iam delete-policy \
    --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/AWSLoadBalancerControllerIAMPolicy

kubectl delete -k github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master

eksctl delete fargateprofile \
  --name game-2048 \
  --cluster eksworkshop-eksctl
```

# Migrate workloads to EKS
![Cluster Creation Workflow](imgs/counter-app.gif "Cluster Creation Workflow")

In this module we will migrate a workload from a self managed kind cluster to an EKS cluster. The workload will have a stateless frontend and a stateful database backend. You’ll need to follow the steps to create a Cloud9 workspace. Make sure you update your IAM permissions with an eksworkshop-admin role.

When you create your Cloud9 instance you should select an instance size with at least 8 GB of memory (eg m5.large) because we are going to create a kind cluster we will migrate workloads from.

We’ll need a few environment variables throughout this section so let’s set those up now. Unless specified all commands should be run from your Cloud9 instance.
```
export CLUSTER=eksworkshop
export AWS_ZONE=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
export AWS_REGION=${AWS_ZONE::-1}
export AWS_DEFAULT_REGION=${AWS_REGION}
export ACCOUNT_ID=$(aws sts get-caller-identity --query 'Account' --output text)
export INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
export MAC=$(curl -s http://169.254.169.254/latest/meta-data/mac)
export SECURITY_GROUP=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/${MAC}/security-group-ids)
export SUBNET=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/${MAC}/subnet-id)
export VPC=$(curl -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/${MAC}/vpc-id)
export IP=$(ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')

printf "export CLUSTER=$CLUSTER\nexport ACCOUNT_ID=$ACCOUNT_ID\nexport AWS_REGION=$AWS_REGION\nexport AWS_DEFAULT_REGION=${AWS_REGION}\nexport AWS_ZONE=$AWS_ZONE\nexport INSTANCE_ID=$INSTANCE_ID\nexport MAC=$MAC\nexport SECURITY_GROUP=$SECURITY_GROUP\nexport SUBNET=$SUBNET\nexport VPC=$VPC\nexport IP=$IP" | tee -a ~/.bash_profile
. ~/.bash_profile
```

Now we can expand the Cloud9 root volume
```
curl -sL 'https://eksworkshop.com/intermediate/200_migrate_to_eks/resize-ebs.sh' | bash
```

Install kubectl, kind, aws-iam-authenticator, eksctl and update aws
```
# Install kubectl
curl -sLO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm -f ./kubectl

# install eksctl
curl -sLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"
tar xz -C /tmp -f "eksctl_$(uname -s)_amd64.tar.gz"
sudo install -o root -g root -m 0755 /tmp/eksctl /usr/local/bin/eksctl
rm -f ./"eksctl_$(uname -s)_amd64.tar.gz"

# install aws-iam-authenticator
curl -sLO "https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/aws-iam-authenticator"
sudo install -o root -g root -m 0755 aws-iam-authenticator /usr/local/bin/aws-iam-authenticator
rm -f ./aws-iam-authenticator

# install kind
curl -sLo kind "https://kind.sigs.k8s.io/dl/v0.11.0/kind-linux-amd64"
sudo install -o root -g root -m 0755 kind /usr/local/bin/kind
rm -f ./kind

# install awscliv2
curl -sLo "awscliv2.zip" "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip"
unzip awscliv2.zip
sudo ./aws/install
rm -rf ./awscliv2.zip ./aws

# setup tab completion
/usr/local/bin/kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl >/dev/null
/usr/local/bin/eksctl completion bash | sudo tee /etc/bash_completion.d/eksctl >/dev/null

echo 'source /usr/share/bash-completion/bash_completion' >> $HOME/.bashrc
. $HOME/.bashrc
```

Once you have everything done use eksctl to create an EKS cluster with the following command
```
eksctl create cluster --name $CLUSTER \
    --managed --enable-ssm
```

## Create the KIND cluster
While our EKS cluster is being created we can create a kind cluster locally. Before we create one lets make sure our network rules are set up

This is going to manually create some iptables rules to route traffic to your Cloud9 instance. If you reboot the VM you will have to run these commands again as they persistent.
```
echo 'net.ipv4.conf.all.route_localnet = 1' | sudo tee /etc/sysctl.conf
sudo sysctl -p /etc/sysctl.conf
sudo iptables -t nat -A PREROUTING -p tcp -d 169.254.170.2 --dport 80 -j DNAT --to-destination 127.0.0.1:51679
sudo iptables -t nat -A OUTPUT -d 169.254.170.2 -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 51679
```

Create a config file for the kind cluster
```
cat > kind.yaml <<EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.19.11@sha256:07db187ae84b4b7de440a73886f008cf903fcf5764ba8106a9fd5243d6f32729
  extraPortMappings:
  - containerPort: 30000
    hostPort: 30000
  - containerPort: 30001
    hostPort: 30001
EOF
```
Create the local kind cluster
```
kind create cluster --config kind.yaml
```

Set the default context to the EKS cluster.
```
kubectl config use-context "${INSTANCE_ID}@${CLUSTER}.${AWS_REGION}.eksctl.io"
```

## Deploy Counter APp to KIND
Once the kind cluster is ready we can check it with
```
kubectl --context kind-kind get nodes
```

Deploy our postgres database to the cluster. First create a ConfigMap to initialize an empty database and then create a PersistentVolume on hostPath to store the data.
```
cat <<EOF | kubectl --context kind-kind apply -f -
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: postgres-config
  labels:
    app: postgres
data:
  POSTGRES_PASSWORD: supersecret
  init: |
    CREATE TABLE importantdata (
    id int4 PRIMARY KEY,
    count int4 NOT NULL
    );

    INSERT INTO importantdata (id , count) VALUES (1, 0);
---
kind: PersistentVolume
apiVersion: v1
metadata:
  name: postgres-pv-volume
  labels:
    type: local
    app: postgres
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/data"
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: postgres-pv-claim
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
EOF
```

Now deploy a Postgres StatefulSet and service. You can see we mount the ConfigMap and PersistentVolumeClaim
```
cat <<EOF | kubectl --context kind-kind apply -f -
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  replicas: 1
  serviceName: postgres
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      terminationGracePeriodSeconds: 5
      containers:
        - name: postgres
          image: postgres:13
          imagePullPolicy: "IfNotPresent"
          ports:
            - containerPort: 5432
          envFrom:
            - configMapRef:
                name: postgres-config
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgredb
            - mountPath: /docker-entrypoint-initdb.d
              name: init
          resources:
            requests:
              memory: "64Mi"
              cpu: "250m"
            limits:
              memory: "128Mi"
              cpu: "500m"
      volumes:
        - name: postgredb
          persistentVolumeClaim:
            claimName: postgres-pv-claim
        - name: init
          configMap:
            name: postgres-config
            items:
            - key: init
              path: init.sql
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  type: ClusterIP
  ports:
   - port: 5432
  selector:
   app: postgres
EOF
```

Finally deploy the counter frontend and NodePort service
```
cat <<EOF | kubectl --context kind-kind apply -f -
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter
  labels:
    app: counter
spec:
  replicas: 2
  selector:
    matchLabels:
      app: counter
  template:
    metadata:
      labels:
        app: counter
    spec:
      containers:
      - name: counter
        image: public.ecr.aws/aws-containers/stateful-counter:latest
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "16Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: counter-service
spec:
  type: NodePort
  selector:
    app: counter
  ports:
    - port: 8000
      name: http
      nodePort: 30000
EOF
```

You can verify that your application and database is running with
```
kubectl --context kind-kind get pods
```

## Expose Counter APP from KIND
An app that’s not exposed isn’t very useful. We’ll manually create a load balancer to expose the app.

The first thing you need to do is get your local computer’s public ip address. Open a new browser tab and to icanhasip.com and copy the IP address.

Go back to the Cloud9 shell and save that IP address as an environment variable
```
export PUBLIC_IP=#YOUR PUBLIC IP
```

Now allow your IP address to access port 80 of your Cloud9 instance’s security group and allow traffic on the security group for a load balancer.
```
aws ec2 authorize-security-group-ingress \
    --group-id $SECURITY_GROUP \
    --protocol tcp \
    --port 80 \
    --cidr ${PUBLIC_IP}/25

aws ec2 authorize-security-group-ingress \
    --group-id $SECURITY_GROUP \
    --protocol -1 \
    --source-group $SECURITY_GROUP
```
Create an Application Load Balancer (ALB) in the same security group and subnet. An ALB needs to be spread across a minimum of two subnets.
```
export ALB_ARN=$(aws elbv2 create-load-balancer \
    --name counter \
    --subnets $(aws ec2 describe-subnets \
        --filters "Name=vpc-id,Values=$VPC" \
        --query 'Subnets[*].SubnetId' \
        --output text) \
    --type application --security-groups $SECURITY_GROUP \
    --query 'LoadBalancers[0].LoadBalancerArn' \
    --output text)
```
Create a target group
```
export TG_ARN=$(aws elbv2 create-target-group \
    --name counter-target --protocol HTTP \
    --port 30000 --target-type instance \
    --vpc-id ${VPC} --query 'TargetGroups[0].TargetGroupArn' \
    --output text)
```
Register our node to the TG
```
aws elbv2 register-targets \
    --target-group-arn ${TG_ARN} \
    --targets Id=${INSTANCE_ID}
```
Create a listener and default action
```
aws elbv2 wait load-balancer-available \
    --load-balancer-arns $ALB_ARN \
    && export ALB_LISTENER=$(aws elbv2 create-listener \
    --load-balancer-arn ${ALB_ARN} \
    --port 80 --protocol HTTP \
    --default-actions Type=forward,TargetGroupArn=${TG_ARN} \
    --query 'Listeners[0].ListenerArn' \
    --output text)
```
Our local counter app should now be exposed from the ALB
```
echo "http://"$(aws elbv2 describe-load-balancers \
    --load-balancer-arns $ALB_ARN \
    --query 'LoadBalancers[0].DNSName' --output text)
```

Make sure you click the button a lot because that’s the important data we’re going to migrate to EKS later.
![Cluster Creation Workflow](imgs/counter-app_1.gif "Cluster Creation Workflow")

## Configure EKS Cluster
We created an EKS cluster cluster with a managed node group and OIDC. For Postgres persistent storage we’re going to use a host path for the sake of this workshop but it would be advised to use Amazon Elastic File System (EFS) because it’s a regional storage service. If the Postgres pod moves availability zones data will still be available.

To let traffic cross between EKS and Cloud9 we need to create a VPC peer between our Cloud9 instance and our EKS cluster.
```
export EKS_VPC=$(aws eks describe-cluster \
    --name ${CLUSTER} \
    --query "cluster.resourcesVpcConfig.vpcId" \
    --output text)

export PEERING_ID=$(aws ec2 create-vpc-peering-connection \
    --vpc-id $VPC --peer-vpc-id $EKS_VPC \
    --query 'VpcPeeringConnection.VpcPeeringConnectionId' \
    --output text)

aws ec2 accept-vpc-peering-connection \
    --vpc-peering-connection-id $PEERING_ID

aws ec2 modify-vpc-peering-connection-options \
    --vpc-peering-connection-id $PEERING_ID \
    --requester-peering-connection-options '{"AllowDnsResolutionFromRemoteVpc":true}' \
    --accepter-peering-connection-options '{"AllowDnsResolutionFromRemoteVpc":true}'
```
Allow traffic from the EKS from the EKS VPC and Security group to our Cloud9 instance
```
export EKS_SECURITY_GROUP=$(aws cloudformation list-exports \
    --query "Exports[*]|[?Name=='eksctl-$CLUSTER-cluster::SharedNodeSecurityGroup'].Value" \
    --output text)

export EKS_CIDR_RANGES=$(aws ec2 describe-subnets \
    --filter "Name=vpc-id,Values=$EKS_VPC" \
    --query 'Subnets[*].CidrBlock' \
    --output text)

for CIDR in $(echo $EKS_CIDR_RANGES); do
    aws ec2 authorize-security-group-ingress \
        --group-id $SECURITY_GROUP \
        --ip-permissions IpProtocol=tcp,FromPort=1024,ToPort=65535,IpRanges="[{CidrIp=$CIDR}]"
done

export CIDR_RANGES=$(aws ec2 describe-subnets \
    --filter "Name=vpc-id,Values=$VPC" \
    --query 'Subnets[*].CidrBlock' \
    --output text)

for CIDR in $(echo $CIDR_RANGES); do
    aws ec2 authorize-security-group-ingress \
        --group-id $EKS_SECURITY_GROUP \
        --ip-permissions IpProtocol=tcp,FromPort=1024,ToPort=65535,IpRanges="[{CidrIp=$CIDR}]"
done
```

Finally create routes in both VPCs to route traffic
```
export CIDR_BLOCK_1=$(aws ec2 describe-vpc-peering-connections \
    --query "VpcPeeringConnections[?VpcPeeringConnectionId=='$PEERING_ID'].AccepterVpcInfo.CidrBlock" \
    --output text)

export CIDR_BLOCK_2=$(aws ec2 describe-vpc-peering-connections \
    --query "VpcPeeringConnections[?VpcPeeringConnectionId=='$PEERING_ID'].RequesterVpcInfo.CidrBlock" \
    --output text)

export EKS_RT=$(aws cloudformation list-stack-resources \
    --query "StackResourceSummaries[?LogicalResourceId=='PublicRouteTable'].PhysicalResourceId" \
    --stack-name eksctl-${CLUSTER}-cluster \
    --output text)

export RT=$(aws ec2 describe-route-tables \
    --filter "Name=vpc-id,Values=${VPC}" \
    --query 'RouteTables[0].RouteTableId' \
    --output text)

aws ec2 create-route \
    --route-table-id $EKS_RT \
    --destination-cidr-block $CIDR_BLOCK_2 \
    --vpc-peering-connection-id $PEERING_ID

aws ec2 create-route \
    --route-table-id $RT \
    --destination-cidr-block $CIDR_BLOCK_1 \
    --vpc-peering-connection-id $PEERING_ID
```

Now that traffic will route between our clusters we can deploy our application to the EKS cluster.

## Deploy Counter APP to EKS
Now it’s time to migrate our app to EKS. We’re going to do this in two stages.

First we’ll move the frontend component but have it talk to the database in our old cluster. Then we’ll set up the database in EKS, migrate the data, and configure the frontend to use it instead.

The counter app deployment and service is the same as it was in kind except we added two environment varibles for the `DB_HOST` and `DB_PORT` and the service type is LoadBalancer instead of NodePort.
```
cat <<EOF | kubectl apply -f -
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter
  labels:
    app: counter
spec:
  replicas: 2
  selector:
    matchLabels:
      app: counter
  template:
    metadata:
      labels:
        app: counter
    spec:
      containers:
      - name: counter
        image: public.ecr.aws/aws-containers/stateful-counter:latest
        env:
        - name: DB_HOST
          value: $IP
        - name: DB_PORT
          value: "30001"
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "16Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: counter
spec:
  ports:
    - port: 80
      targetPort: 8000
  type: LoadBalancer
  selector:
    app: counter
EOF
```

Now create a postgres-external service in kind that exposes postgres on a NodePort.
```
cat <<EOF | kubectl --context kind-kind apply -f -
---
apiVersion: v1
kind: Service
metadata:
  name: postgres-external
  labels:
    app: postgres
spec:
  type: NodePort
  ports:
    - port: 5432
      nodePort: 30001
  selector:
   app: postgres
EOF
```

Now you should be able to get the endpoint for your load balancer and when you load the counter app the same count will be shown in the app.
```
kubectl get svc
```

## Deploying database to EKS
The final step is to move the database from our kind cluster into EKS. There are lots of different options for how you might want to migrate application state. In many cases using an external database such as Amazon Relational Database Service (RDS) is a great fit.

For production data you’ll want to set up a way where you can verify correctness of your state or automatic syncing between environments. For this workshop we’re going to manually move our database state.

The first thing we need to do is create a Postgres database with hostPath persistent storage in Kubernetes. We’ll use the exact same ConfigMap from kind to generate an empty database first.

All of the config for Postgres is the same as it was for kind
```
cat <<EOF | kubectl apply -f -
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: postgres-config
  labels:
    app: postgres
data:
  POSTGRES_PASSWORD: supersecret
  init: |
    CREATE TABLE importantdata (
    id int4 PRIMARY KEY,
    count int4 NOT NULL
    );

    INSERT INTO importantdata (id , count) VALUES (1, 0);
---
kind: PersistentVolume
apiVersion: v1
metadata:
  name: postgres-pv-volume
  labels:
    type: local
    app: postgres
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/data"
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: postgres-pv-claim
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
EOF
```

Deploy the postgres StatefulSet
```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  replicas: 1
  serviceName: postgres
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      terminationGracePeriodSeconds: 5
      containers:
        - name: postgres
          image: postgres:13
          imagePullPolicy: "IfNotPresent"
          ports:
            - containerPort: 5432
          envFrom:
            - configMapRef:
                name: postgres-config
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgredb
            - mountPath: /docker-entrypoint-initdb.d
              name: init
          resources:
            requests:
              memory: "64Mi"
              cpu: "250m"
            limits:
              memory: "128Mi"
              cpu: "500m"
      volumes:
        - name: postgredb
          persistentVolumeClaim:
            claimName: postgres-pv-claim
        - name: init
          configMap:
            name: postgres-config
            items:
            - key: init
              path: init.sql
EOF
```

Backup the data from our kind Postgres database and restore it to EKS using standard postgres tools.
```
kubectl --context kind-kind exec -t postgres-0 -- pg_dumpall -c -U postgres > postgres_dump.sql
```

Restore database
```
cat postgres_dump.sql | kubectl exec -i postgres-0 -- psql -U postgres
```

Now we can deploy a postgres service inside EKS to point to the new database endpoint. This is the exact same postgres service we deployed to kind.
```
cat <<EOF | kubectl apply -f -
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  type: ClusterIP
  ports:
   - port: 5432
  selector:
   app: postgres
EOF
```

Finally we need to update the counter application to remove the two environment variables we added for the external database.
```
cat <<EOF | kubectl apply -f -
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter
  labels:
    app: counter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: counter
  template:
    metadata:
      labels:
        app: counter
    spec:
      containers:
      - name: counter
        image: public.ecr.aws/aws-containers/stateful-counter:latest
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "16Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "500m"
EOF
```
Open your browser to this link
```
echo "http://"$(kubectl get svc counter --output jsonpath='{.status.loadBalancer.ingress[0].hostname}')
```

## cleanup
Delete EKS resources
```
kubectl delete svc/counter svc/postgres deploy/counter statefulset/postgres
```

Delete ALB
```
aws elbv2 delete-listener \
    --listener-arn $ALB_LISTENER

aws elbv2 delete-load-balancer \
    --load-balancer-arn $ALB_ARN

aws elbv2 delete-target-group \
    --target-group-arn $TG_ARN
```
Delete VPC peering
```
aws ec2 delete-vpc-peering-connection \
    --vpc-peering-connection-id $PEERING_ID
```
Delete EKS cluster
```
eksctl delete cluster --name $CLUSTER
```

# Deploy Jenkins
In this module, we will deploy Jenkins using the Helm package manager we installed in the Helm module and the OIDC identity provider we setup in the IAM Roles for Service Accounts module.

## Codecommit repository, access and code
We’ll start by creating a CodeCommit repository to store our example application. This repository will store our application code and Jenkinsfile.
```
aws codecommit create-repository --repository-name eksworkshop-app
```

We’ll create an IAM user with our HTTPS Git credentials for AWS CodeCommit to clone our repository and to push additional commits. This user needs an IAM Policy for access to CodeCommit.
```
aws iam create-user \
  --user-name git-user

aws iam attach-user-policy \
  --user-name git-user \
  --policy-arn arn:aws:iam::aws:policy/AWSCodeCommitPowerUser

aws iam create-service-specific-credential \
  --user-name git-user --service-name codecommit.amazonaws.com \
  | tee /tmp/gituser_output.json

GIT_USERNAME=$(cat /tmp/gituser_output.json | jq -r '.ServiceSpecificCredential.ServiceUserName')
GIT_PASSWORD=$(cat /tmp/gituser_output.json | jq -r '.ServiceSpecificCredential.ServicePassword')
CREDENTIAL_ID=$(cat /tmp/gituser_output.json | jq -r '.ServiceSpecificCredential.ServiceSpecificCredentialId')
```
The repository will require some initial code so we’ll clone the repository and add a simple Go application.
```
sudo pip install git-remote-codecommit

git clone codecommit::${AWS_REGION}://eksworkshop-app
cd eksworkshop-app
```

server.go contains our simple application.
```
cat << EOF > server.go

package main

import (
    "fmt"
    "net/http"
)

func helloWorld(w http.ResponseWriter, r *http.Request){
    fmt.Fprintf(w, "Hello World")
}

func main() {
    http.HandleFunc("/", helloWorld)
    http.ListenAndServe(":8080", nil)
}
EOF
```

server_test.go contains our unit tests.
```
cat << EOF > server_test.go

package main

import (
	"net/http"
	"net/http/httptest"
	"testing"
)

func Test_helloWorld(t *testing.T) {
	req, err := http.NewRequest("GET", "http://domain.com/", nil)
	if err != nil {
		t.Fatal(err)
	}

	res := httptest.NewRecorder()
	helloWorld(res, req)

	exp := "Hello World"
	act := res.Body.String()
	if exp != act {
		t.Fatalf("Expected %s got %s", exp, act)
	}
}

EOF
```

The Jenkinsfile will contain our pipeline declaration, the additional containers in our build agent pods, and which container will be used for each step of the pipeline.
```
cat << EOF > Jenkinsfile
pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: golang
    image: golang:1.13
    command:
    - cat
    tty: true
"""
    }
  }
  stages {
    stage('Run tests') {
      steps {
        container('golang') {
          sh 'go test'
        }
      }
    }
    stage('Build') {
        steps {
            container('golang') {
              sh 'go build -o eksworkshop-app'
              archiveArtifacts "eksworkshop-app"
            }

        }
    }

  }
}

EOF
```

We’ll add the code our code, commit the change, and then push the code to our repository.
```
git add --all && git commit -m "Initial commit." && git push
cd ~/environment
```

## Creating the Jenkins Service Account
We’ll create a service account for Kubernetes to grant to pods if they need to perform CodeCommit API actions (e.g. GetCommit, ListBranches). This will allow Jenkins to respond to new repositories, branches, and commits.

If you have not completed the IAM Roles for Service Accounts lab, please complete the Create an OIDC identity provider step now. You do not need to complete any other sections of that lab.
```
eksctl create iamserviceaccount \
    --name jenkins \
    --namespace default \
    --cluster eksworkshop-eksctl \
    --attach-policy-arn arn:aws:iam::aws:policy/AWSCodeCommitPowerUser \
    --approve \
    --override-existing-serviceaccounts
```

## Deploy Jenkins
### Install Jenkins
We’ll begin by creating the values.yaml to declare the configuration of our Jenkins installation.
```
cat << EOF > values.yaml
---
controller:
  # Used for label app.kubernetes.io/component
  componentName: "jenkins-controller"
  image: "jenkins/jenkins"
  tag: "2.289.2-lts-jdk11"
  additionalPlugins:
    - aws-codecommit-jobs:0.3.0
    - aws-java-sdk:1.11.995
    - junit:1.51
    - ace-editor:1.1
    - workflow-support:3.8
    - pipeline-model-api:1.8.5
    - pipeline-model-definition:1.8.5
    - pipeline-model-extensions:1.8.5
    - workflow-job:2.41
    - credentials-binding:1.26
    - aws-credentials:1.29
    - credentials:2.5
    - lockable-resources:2.11
    - branch-api:2.6.4
  resources:
    requests:
      cpu: "1024m"
      memory: "4Gi"
    limits:
      cpu: "4096m"
      memory: "8Gi"
  javaOpts: "-Xms4000m -Xmx4000m"
  servicePort: 80
  serviceType: LoadBalancer
agent:
  Enabled: false
rbac:
  create: true
serviceAccount:
  create: false
  name: "jenkins"
EOF
```

Now we’ll use the helm cli to create the Jenkins server as we’ve declared it in the values.yaml file.
```
helm install cicd stable/jenkins -f values.yaml
```

The output of this command will give you some additional information such as the admin password and the way to get the host name of the ELB that was provisioned.

Let’s give this some time to provision and while we do let’s watch for pods to boot.
```
kubectl get pods -w
```
You should see the pods in init, pending or running state.

Once this changes to running we can get the load balancer address.
```
export SERVICE_IP=$(kubectl get svc --namespace default cicd-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")

echo http://$SERVICE_IP/login
```
This service was configured with a LoadBalancer so, an AWS Elastic Load Balancer (ELB) is launched by Kubernetes for the service. The EXTERNAL-IP column contains a value that ends with “elb.amazonaws.com” - the full value is the DNS address.

When the front-end service is first deployed, it can take up to several minutes for the ELB to be created and DNS updated. During this time the link above may display a “site unreachable” message. To check if the instances are in service, follow this deep link to the load balancer console. On the load balancer select the instances tab and ensure that the instance status is listed as “InService” before proceeding to the jenkins login page.


## Logging In
Now that we have the ELB address of your jenkins instance we can go an navigate to that address in another window.

![Cluster Creation Workflow](imgs/jenkins-login.png "Cluster Creation Workflow")
From here we can log in using:

|Username |	Password |
|--------|-----------|
|admin|	command from below|
```
printf $(kubectl get secret --namespace default cicd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

The output of this command will give you the default password for your admin user. Log into the jenkins login screen using these credentials.

## Setup Multibranch Projects
After logging into the Jenkins web console, we’re ready to add our eksworkshop-app repository. Start by selecting New Item in the menu on the left side.

![Cluster Creation Workflow](imgs/jenkins-newitem.png "Cluster Creation Workflow")

Set the name of the item to codecommit and select the AWS Code commit item type.

![Cluster Creation Workflow](imgs/jenkins-codecommititem.png "Cluster Creation Workflow")

In your Cloud9 workspace, execute the following commands to get your Git username and password.
```
echo $GIT_USERNAME
echo $GIT_PASSWORD
```

Back to Jenkins. In the Projects section, to the right of Code Commit Credentials, select Add then CodeCommit.

![Cluster Creation Workflow](imgs/jenkins-new-credentials.png "Cluster Creation Workflow")

Set the Username and Password to the corresponding values from the previous command and click Add.

![Cluster Creation Workflow](imgs/jenkins-gitcredentials.png "Cluster Creation Workflow")

Confirm your current AWS Region.

```
echo https://codecommit.$AWS_REGION.amazonaws.com
```

Copy that value to theURL field under project and select your use from the Code Commit Credentials DropDown menu.
![Cluster Creation Workflow](imgs/jenkins-projectsetup.png "Cluster Creation Workflow")

Select Save at the bottom left of the screen. Jenkins will begin executing the pipelines in repositories and branches that contain a Jenkinsfile.

![Cluster Creation Workflow](imgs/jenkins-eksworkshop-app-master.png "Cluster Creation Workflow")

## cleanup
To uninstall Jenkins and cleanup the service account and CodeCommit repository run:
```
helm uninstall cicd

aws codecommit delete-repository \
    --repository-name eksworkshop-app

aws iam detach-user-policy \
    --user-name git-user \
    --policy-arn arn:aws:iam::aws:policy/AWSCodeCommitPowerUser

aws iam delete-service-specific-credential \
    --user-name git-user \
    --service-specific-credential-id $CREDENTIAL_ID

aws iam delete-user \
    --user-name git-user

eksctl delete iamserviceaccount \
    --name jenkins \
    --namespace default \
    --cluster eksworkshop-eksctl

rm -rf ~/environment/eksworkshop-app
rm ~/environment/values.yaml

sudo pip uninstall -y git-remote-codecommit
```


# Continuous Delivery with Spinnaker
[Spinnaker](https://spinnaker.io/concepts/) is an open-source, multi-cloud continuous delivery platform, originally developed by Netflix, that helps you release software changes rapidly and reliably. Development team can focus on just application development and leave ops provisioning to Spinnaker for automating reinforcement of business and regulatory requirements. Spinnaker supports several CI systems and build tools like CodeBuild, Jenkins. You can integrate Spinnaker for configuring Artifacts from Git, Amazon S3, Amazon ECR etc.

In this module, we will focus on Application Deployment using Spinnaker:

- How to install Spinnaker and configure Spinnaker services using Kubernetes Operator in EKS
- Build simple Spinnaker CD pipeline
  - Stage - Add Ngnix deployment artifact manifest
  - Testing -
    - Manually trigger the pipeline
    - Test the deployment
- Build Helm-based Spinnaker CD Pipeline
  - Configuration
    - Trigger: Continuous Delivery will be triggered automatically based on new image upload into ECR
  - Stage 1 - Bake the manifest from GitHub repo for deployment using Helm
  - Stage 2 - Deploy the Helm artifact to EKS
  - Testing -
    - Upload container image to ECR to trigger the deployment pipeline
    - Test the deployment
![Cluster Creation Workflow](imgs/architecture-s.png "Cluster Creation Workflow")

## Spinnaker Overview
### Spinnaker Architecture
You can also see the detailed architecture of spinnaker at [Armory docs](https://docs.armory.io/docs/overview/architecture/).

![Cluster Creation Workflow](imgs/architecture.png "Cluster Creation Workflow")

- Deck: Browser-based UI for Spinnaker.

- Gate: API callers and Spinnaker UI communicate to Spinnaker server via this API gateway called Gate.

- Orca: Pipelines and other ad-hoc operations are managed by this orchestration engine called Orca.

- Clouddriver: Indexing and Caching of deployed resources are taken care by Clouddriver. It also facilitates calls to cloud providers like AWS, GCE, and Azure.

- Echo: It is responsible for sending notifications, it also acts as incoming webhook.

- Igor: It is used to trigger pipelines via continuous integration jobs in systems like Jenkins and Travis CI, and it allows Jenkins/Travis stages to be used in pipelines.

- Front50: It’s the metadata store of Spinnaker. It persists metadata for all resources which include pipelines, projects, applications and notifications.

- Rosco: Rosco bakes machine images (AWS AMIs, Azure VM images, GCE images).

- Rush: It is Spinnaker’s script excution engine.

### Spinnaker Concepts
Spinnaker provides two core sets of features:

- Application management (a.k.a. infrastructure management) You use Spinnaker’s [application management](https://spinnaker.io/concepts/#application-management-aka-infrastructure-management) features to view and manage your cloud resources.

- Application deployment You use Spinnaker’s application deployment features to construct and manage continuous delivery workflows.

In this workshop, we are only focussing on Application Deployment so lets deep dive into this feature. More details on Spinnaker Nomenclature and Naming Conventions can be found at [Armory docs](https://docs.armory.io/docs/overview/naming-conventions/).

- Pipeline: The pipeline is the key deployment management construct in Spinnaker. It consists of a sequence of actions, known as stages. You can pass parameters from stage to stage along the pipeline.

![Cluster Creation Workflow](imgs/pipelines.png "Cluster Creation Workflow")

- Stage: A Stage in Spinnaker is a collection of sequential Tasks and composed Stages that describe a higher-level action the Pipeline will perform either linearly or in parallel.

- Task: A Task in Spinnaker is an automatic function to perform.

- Deployment Strategies: Spinnaker treats cloud-native deployment strategies as first class constructs, handling the underlying orchestration such as verifying health checks, disabling old server groups and enabling new server groups. Spinnaker supports the red/black (a.k.a. blue/green) strategy, with rolling red/black and canary strategies in active development.

## Install Spinnaker Operator
There are several methods to install open source Spinnaker on EKS/Kubernetes:

- [Halyard](https://spinnaker.io/setup/install/)
- [Operator](https://github.com/armory/spinnaker-operator)
- [Kleat](https://github.com/spinnaker/kleat) intended to replace Halyard (under active development)

In this workshop we will be using Spinnaker Operator, a Kubernetes Operator for managing Spinnaker, built by Armory. The Operator makes managing Spinnaker, which runs in Kubernetes, dramatically simpler and more automated, while introducing new Kubernetes-native features. The current tool (Halyard) involved significant manual processes and requires Spinnaker domain expertise.

In contrast, the Operator lets you treat Spinnaker as just another Kubernetes deployment, which makes installing and managing Spinnaker easy and reliable. The Operator unlocks the scalability of a GitOps workflow by defining Spinnaker configurations in a code repository rather than in hal commands.

More details on the benefits of Sipnnaker Operator can be found in Armory Docs

### PreRequisites
- We assume that we have an existing EKS Cluster eksworkshop-eksctl created from this workshop.

- We also assume that we have increased the disk size on your Cloud9 instance as we need to build docker images for our application.

- [Optional] If you want to use AWS Console to navigate and explore resources in Amazon EKS ensure that you have completed Console Credentials to get full access to the EKS Cluster in the EKS console.

- We also have installed the prequisite for EKS Cluster installation

- And we have also validated the IAM role in use by the Cloud9 IDE

- Ensure you are getting the IAM role that you have attached to Cloud9 IDE when you execute the below command
```
aws sts get-caller-identity
```
```

{
	"Account": "XXXXXXX",
	"UserId": "YYYYYYYY:i-009b2d423b1386a74",
	"Arn": "arn:aws:sts::XXXXXXX:assumed-role/eksworkshop-admin-s/i-009b2d423b1386a74"
}
```

- Check if AWS_REGION and ACCOUNT_ID are set correctly
```
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
test -n "$ACCOUNT_ID" && echo ACCOUNT_ID is "$ACCOUNT_ID" || echo ACCOUNT_ID is not set
```

If not, export the ACCOUNT_ID and AWS_REGION to ENV

```
export ACCOUNT_ID=<your_account_id>
export AWS_REGION=<your_aws_region>
```

### EKS cluster setup
We need bigger instance type for installing spinnaker services, hence we are creating new EKS cluster.

We are also deleting the existng nodegroup nodegroup that was created as part of cluster creation as we need Spinnaker Operator to create the services in the new nodegroup spinnaker
```
cat << EOF > spinnakerworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}

# https://eksctl.io/usage/eks-managed-nodegroups/
managedNodeGroups:
  - name: spinnaker
    minSize: 2
    maxSize: 3
    desiredCapacity: 3
    instanceType: m5.large
    ssh:
      enableSsm: true
    volumeSize: 20
    labels: {role: spinnaker}
    tags:
      nodegroup-role: spinnaker
EOF

eksctl create nodegroup -f spinnakerworkshop.yaml
eksctl delete nodegroup --cluster=eksworkshop-eksctl --name=nodegroup
```

```
.....
.....
.....
2021-05-20 16:45:15 [ℹ]  waiting for at least 2 node(s) to become ready in "spinnaker"
2021-05-20 16:45:15 [ℹ]  nodegroup "spinnaker" has 3 node(s)
2021-05-20 16:45:15 [ℹ]  node "ip-192-y-x-184.${AWS_REGION}.compute.internal" is ready
2021-05-20 16:45:15 [ℹ]  node "ip-192-y-x-128.${AWS_REGION}.compute.internal" is ready
2021-05-20 16:45:15 [ℹ]  node "ip-192-y-x-105.${AWS_REGION}.compute.internal" is ready
2021-05-20 16:45:15 [✔]  created 1 managed nodegroup(s) in cluster "eksworkshop-eksctl"
2021-05-20 16:45:16 [ℹ]  checking security group configuration for all nodegroups
2021-05-20 16:45:16 [ℹ]  all nodegroups have up-to-date configuration
```
Confirm the setup
```
kubectl get nodes
```
```
NAME                                           STATUS   ROLES    AGE     VERSION
ip-192-y-x-184.${AWS_REGION}.compute.internal   Ready    <none>   4m45s   v1.17.12-eks-7684af
ip-192-y-x-128.${AWS_REGION}.compute.internal   Ready    <none>   4m37s   v1.17.12-eks-7684af
ip-192-y-x-105.${AWS_REGION}.compute.internal    Ready    <none>   4m47s   v1.17.12-eks-7684afå
```

### Install Spinnaker CRDs
Pick a release from https://github.com/armory/spinnaker-operator/releases and export that version. Below we are using the latest release of Spinnaker Operator when this workshop was written,
```
export VERSION=1.2.4
echo $VERSION

mkdir -p spinnaker-operator && cd spinnaker-operator
bash -c "curl -L https://github.com/armory/spinnaker-operator/releases/download/v${VERSION}/manifests.tgz | tar -xz"
kubectl apply -f deploy/crds/
```

```
1.2.4
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   621  100   621    0     0   2700      0 --:--:-- --:--:-- --:--:--  2688
100 11225  100 11225    0     0  29080      0 --:--:-- --:--:-- --:--:-- 29080
customresourcedefinition.apiextensions.k8s.io/spinnakeraccounts.spinnaker.io created
customresourcedefinition.apiextensions.k8s.io/spinnakerservices.spinnaker.io created
```

### Install Spinnaker Operator
Install operator in namespace spinnaker-operator. We have used Cluster mode for the operator that works across namespaces and requires a ClusterRole to perform validation.
```
kubectl create ns spinnaker-operator
kubectl -n spinnaker-operator apply -f deploy/operator/cluster
```

```
namespace/spinnaker-operator created
deployment.apps/spinnaker-operator created
clusterrole.rbac.authorization.k8s.io/spinnaker-operator-role created
clusterrolebinding.rbac.authorization.k8s.io/spinnaker-operator-binding created
serviceaccount/spinnaker-operator created
```

Make sure the Spinnaker-Operator pod is running

This may take couple of minutes
```
kubectl get pod -n spinnaker-operator
```

```
NAME                                  READY   STATUS    RESTARTS   AGE
spinnaker-operator-6d95f9b567-tcq4w   2/2     Running   0          82s
```

## Artifact Configuration
Lets configure all the artifacts and storage for Spinnaker services that we will need for our usecase. We will adding all the configuration to the file located at deploy/spinnaker/basic/spinnakerservice.yml which got created by Spinnaker Operator install in previous module.

### Configure Spinnaker Release Version
Pick a release from https://spinnaker.io/community/releases/versions/ and export that version. Below we are using the latest Spinnaker release when this workshop was written,
```
export SPINNAKER_VERSION=1.25.4
```

Open the SpinnakerService manifest located at deploy/spinnaker/basic/spinnakerservice.yml, and change below for Spinnaker Version

```
  version: $SPINNAKER_VERSION   # the version of Spinnaker to be deployed
```

### Configure S3 Artifact
We will configure Spinnaker to access an bucket as a source of artifacts. Spinnaker stages such as a Deploy Manifest read configuration from S3 files directly. Lets enable S3 as an artifact source.

Spinnaker requires an external storage provider for persisting our Application settings and configured Pipelines. In this workshop we will be using S3 as a storage source means that Spinnaker will store all of its persistent data in a Bucket.

- Create S3 Bucket first

You can create S3 bucket either using Admin Console or using AWS CLI (Use one of the option from below)

- Using Admin Console

Go to AWS Console »> S3 and create the bucket as below
![Cluster Creation Workflow](imgs/s3bucket.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/s3bucketdetail.png "Cluster Creation Workflow")

- Using AWS CLI
```
export S3_BUCKET=spinnaker-workshop-$(cat /dev/urandom | LC_ALL=C tr -dc "[:alpha:]" | tr '[:upper:]' '[:lower:]' | head -c 10)
aws s3 mb s3://$S3_BUCKET
aws s3api put-public-access-block \
--bucket $S3_BUCKET \
--public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
echo $S3_BUCKET
```
### Set up environment variables

AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are the AWS profile credentials for the user who has created the above S3 bucket.

```
export S3_BUCKET=<your_s3_bucket>
export AWS_ACCESS_KEY_ID=<your_access_key>
export AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
```

### Configure persistentStorage
Open the SpinnakerService manifest located at deploy/spinnaker/basic/spinnakerservice.yml, then update the section spec.spinnakerConfig.config as below.

```
  persistentStorage:
    persistentStoreType: s3
    s3:
      bucket: $S3_BUCKET
      rootFolder: front50
      region: $AWS_REGION
      accessKeyId: $AWS_ACCESS_KEY_ID
      secretAccessKey: $AWS_SECRET_ACCESS_KEY
```

### Configure ECR Artifact
Amazon ECR requires access tokens to access the images and those access tokens expire after a time. In order to automate updating the token, use a sidecar container with a script that does it for you. Since both Clouddriver and the sidecar container need access to the ECR access token, we will use a shared volume to store the access token.

The sidecar needs to be able to request an access token from ECR. The Spinnaker installation must have the AmazonEC2ContainerRegistryReadOnly policy attached to the role assigned in order to request and update the required access token.

- Create ECR Repository
Clone Application Git Repo
```
cd ~/environment
git clone https://github.com/aws-containers/eks-microservice-demo.git
cd eks-microservice-demo
```

We need to push a test container image to the newly created ECR repository. The resaon being, empty ECR respository does not show up in the Spinnaker UI when we set up the trigger in pipeline.
```
export ECR_REPOSITORY=eks-microservice-demo/test
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
aws ecr describe-repositories --repository-name $ECR_REPOSITORY >/dev/null 2>&1 || \
  aws ecr create-repository --repository-name $ECR_REPOSITORY >/dev/null
TARGET=$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPOSITORY:latest
docker build -t $TARGET apps/detail
docker push $TARGET
```

### Create a configmap
Below we are creating the spinnaker namespace where all the Spinnaker services will be deployed and also creating configmap for ECR token.
```
kubectl create ns spinnaker

cat << EOF > config.yaml
interval: 30m # defines refresh interval
registries: # list of registries to refresh
  - registryId: "$ACCOUNT_ID"
    region: "$AWS_REGION"
    passwordFile: "/etc/passwords/my-ecr-registry.pass"
EOF

kubectl -n spinnaker create configmap token-refresh-config --from-file config.yaml
```
```
namespace/spinnaker created
configmap/token-refresh-config created
```

Confirm if configmap is created correctly
```
kubectl describe configmap token-refresh-config -n spinnaker
```

### Add a sidecar for token refresh
Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below snippet under spec.spinnakerConfig.config.
```

      deploymentEnvironment:
        sidecars:
          spin-clouddriver:
          - name: token-refresh
            dockerImage: quay.io/skuid/ecr-token-refresh:latest
            mountPath: /etc/passwords
            configMapVolumeMounts:
            - configMapName: token-refresh-config
              mountPath: /opt/config/ecr-token-refresh

```

Define an ECR Registry
Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below section under spec.spinnakerConfig.
```

      profiles:
        clouddriver:
          dockerRegistry:
            enabled: true
            primaryAccount: my-ecr-registry
            accounts:
            - name: my-ecr-registry
              address: https://$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
              username: AWS
              passwordFile: /etc/passwords/my-ecr-registry.pass
              trackDigests: true
              repositories:
              - $ECR_REPOSITORY
```

Configure Igor
Igor is a is a wrapper API that provides a single point of integration with Continuous Integration (CI) and Source Control Management (SCM) services for Spinnaker. It is responsible for kicking-off jobs and reporting the state of running or completing jobs.

Clouddriver can be configured to poll the ECR registries. When that is the case, igor can then create a poller that will list the registries indexed by clouddriver, check each one for new images and submit events to echo (hence allowing Docker triggers)

Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below section to spec.spinnakerConfig.profiles.
```

      igor:
        docker-registry:
          enabled: true

```

Add GitHub Repository
Set up environment variables
```
export GITHUB_USER=<your_github_username>
export GITHUB_TOKEN=<your_github_accesstoken>
```
Configure GitHub
To access a GitHub repo as a source of artifacts. If you actually want to use a file from the GitHub commit in your pipeline, you’ll need to configure GitHub as an artifact source in Spinnaker.

Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below under section spec.spinnakerConfig.config.
```

      features:
        artifacts: true
      artifacts:
        github:
          enabled: true
          accounts:
          - name: $GITHUB_USER
            token: $GITHUB_TOKEN  # GitHub's personal access token. This fields supports `encrypted` references to secrets.

```

## Add EKS Account
At a high level, Spinnaker operates in the following way when deploying to Kubernetes:

- Spinnaker is configured with one or more “Cloud Provider” Kubernetes accounts (which you can think of as deployment targets)
- For each Kubernetes account, Spinnaker is provided a kubeconfig to connect to that Kubernetes cluster
- The kubeconfig should have the following contents:
  - A Kubernetes kubeconfig cluster
  - A Kubernetes kubeconfig user
  - A Kubernetes kubeconfig context
  - Metadata such as which context to use by default
- Each Kubernetes account is configured in the SpinnakerService manifest under spec.spinnakerConfig.config.providers.kubernetes.accounts key. Each entity   has these (and other) fields:
  - name: A Spinnaker-internal name
  - kubeconfigFile: A file path referencing the contents of the kubeconfig file for connecting to the target cluster.
  - onlySpinnakerManaged: When true, Spinnaker only caches and displays applications that have been created by Spinnaker.
  - namespaces: An array of namespaces that Spinnaker will be allowed to deploy to. If this is left blank, Spinnaker will be allowed to deploy to all namespaces
  - omitNamespaces: If namespaces is left blank, you can blacklist specific namespaces to indicate to Spinnaker that it should not deploy to those namespaces
- If the kubeconfig is properly referenced and available, Operator will take care of the following:
  - Creating a Kubernetes secret containing your kubeconfig in the namespace where Spinnaker lives
  - Dynamically generating a clouddriver.yml file that properly references the kubeconfig from where it is mounted within the Clouddriver container
  - Creating/Updating the Kubernetes Deployment (spin-clouddriver) which runs Clouddriver so that it is aware of the secret and properly mounts it in the Clouddriver pod
Now, lets add a Kubernetes/EKS Account Deployment Target in Spinnaker.

### Download the latest spinnaker-tools release
This tool helps to create the ServiceAccount, ClusterRoleBinding, kubeconfig for the service account for the EKS/Kubernetes account
```
cd ~/environment
git clone https://github.com/armory/spinnaker-tools.git
cd spinnaker-tools
go mod download all
go build
```
```
Cloning into 'spinnaker-tools'...
remote: Enumerating objects: 278, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 278 (delta 0), reused 4 (delta 0), pack-reused 272
Receiving objects: 100% (278/278), 84.72 KiB | 4.71 MiB/s, done.
Resolving deltas: 100% (124/124), done.
```

### Setup environment variables
```
export CONTEXT=$(kubectl config current-context)
export SOURCE_KUBECONFIG=${HOME}/.kube/config
export SPINNAKER_NAMESPACE="spinnaker"
export SPINNAKER_SERVICE_ACCOUNT_NAME="spinnaker-ws-sa"
export DEST_KUBECONFIG=${HOME}/Kubeconfig-ws-sa

echo $CONTEXT
echo $SOURCE_KUBECONFIG
echo $SPINNAKER_NAMESPACE
echo $SPINNAKER_SERVICE_ACCOUNT_NAME
echo $DEST_KUBECONFIG
```

If you do not see output from the above command for all the above Environment Variables, do not proceed to next step

### Create the service account
Create the kubernetes service account with namespace-specific permissions
```
./spinnaker-tools create-service-account   --kubeconfig ${SOURCE_KUBECONFIG}   --context ${CONTEXT}   --output ${DEST_KUBECONFIG}   --namespace ${SPINNAKER_NAMESPACE}   --service-account-name ${SPINNAKER_SERVICE_ACCOUNT_NAME}
```
```

Cloning into 'spinnaker-tools'...
remote: Enumerating objects: 278, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 278 (delta 0), reused 4 (delta 0), pack-reused 272
Receiving objects: 100% (278/278), 84.72 KiB | 4.71 MiB/s, done.
Resolving deltas: 100% (124/124), done.

Getting namespaces ...
Creating service account spinnaker-ws-sa ...
Created ServiceAccount spinnaker-ws-sa in namespace spinnaker
Adding cluster-admin binding to service account spinnaker-ws-sa ...
Created ClusterRoleBinding spinnaker-spinnaker-ws-sa-admin in namespace spinnaker
Getting token for service account ...
Cloning kubeconfig ...
Renaming context in kubeconfig ...
Switching context in kubeconfig ...
Creating token user in kubeconfig ...
Updating context to use token user in kubeconfig ...
Updating context with namespace in kubeconfig ...
Minifying kubeconfig ...
Deleting temp kubeconfig ...
Created kubeconfig file at /home/ec2-user/Kubeconfig-ws-sa
```
### Configure EKS Account
Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below to the section spec.spinnakerConfig.config.
```

        providers:
            dockerRegistry:
              enabled: true
            kubernetes:
              enabled: true
              accounts:
              - name: spinnaker-workshop
                requiredGroupMembership: []
                providerVersion: V2
                permissions:
                dockerRegistries:
                  - accountName: my-ecr-registry
                configureImagePullSecrets: true
                cacheThreads: 1
                namespaces: [spinnaker,detail]
                omitNamespaces: []
                kinds: []
                omitKinds: []
                customResources: []
                cachingPolicies: []
                oAuthScopes: []
                onlySpinnakerManaged: false
                kubeconfigFile: kubeconfig-sp  # File name must match "files" key
              primaryAccount: spinnaker-workshop  # Change to a desired account from the accounts array

```
Open the SpinnakerService manifest located under deploy/spinnaker/basic/spinnakerservice.yml, then add the below section under spec.spinnakerConfig. Replace the <FILE CONTENTS HERE> in below section with kubeconfig file content created from previous step from the location ${HOME}/Kubeconfig-ws-sa.
```

    files:
        kubeconfig-sp: |
           <FILE CONTENTS HERE> # Content from kubeconfig created by Spinnaker Tool

```
Congratulations! You are done with the Spinnaker configuration for all the Spinnaker services! Lets install Spinnaker now.

## Install Spinnaker
By now we have completed our configuration for Spinnaker and the SpinnakerService manifest located at deploy/spinnaker/basic/spinnakerservice.yml should look like below:
```

apiVersion: spinnaker.io/v1alpha2
kind: SpinnakerService
metadata:
  name: spinnaker
spec:
  spinnakerConfig:
    config:
      version: $SPINNAKER_VERSION   # the version of Spinnaker to be deployed
      persistentStorage:
        persistentStoreType: s3
        s3:
          bucket: $S3_BUCKET
          rootFolder: front50
          region: $AWS_REGION
          accessKeyId: $AWS_ACCESS_KEY_ID
          secretAccessKey: $AWS_SECRET_ACCESS_KEY
      deploymentEnvironment:
        sidecars:
          spin-clouddriver:
          - name: token-refresh
            dockerImage: quay.io/skuid/ecr-token-refresh:latest
            mountPath: /etc/passwords
            configMapVolumeMounts:
            - configMapName: token-refresh-config
              mountPath: /opt/config/ecr-token-refresh
      features:
        artifacts: true
      artifacts:
        github:
          enabled: true
          accounts:
          - name: $GITHUB_USER
            token: $GITHUB_TOKEN  # GitHub's personal access token. This fields supports `encrypted` references to secrets.
      providers:
          dockerRegistry:
            enabled: true
          kubernetes:
            enabled: true
            accounts:
            - name: spinnaker-workshop
              requiredGroupMembership: []
              providerVersion: V2
              permissions:
              dockerRegistries:
                - accountName: my-ecr-registry
              configureImagePullSecrets: true
              cacheThreads: 1
              namespaces: [spinnaker,detail]
              omitNamespaces: []
              kinds: []
              omitKinds: []
              customResources: []
              cachingPolicies: []
              oAuthScopes: []
              onlySpinnakerManaged: false
              kubeconfigFile: kubeconfig-sp  # File name must match "files" key
            primaryAccount: spinnaker-workshop  # Change to a desired account from the accounts array
    profiles:
        clouddriver:
          dockerRegistry:
            enabled: true
            primaryAccount: my-ecr-registry
            accounts:
            - name: my-ecr-registry
              address: https://$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
              username: AWS
              passwordFile: /etc/passwords/my-ecr-registry.pass
              trackDigests: true
              repositories:
              - $ECR_REPOSITORY
        igor:
          docker-registry:
            enabled: true
    files:
        kubeconfig-sp: |
          <FILE CONTENTS HERE> # Content from kubeconfig created by Spinnaker Tool
  # spec.expose - This section defines how Spinnaker should be publicly exposed
  expose:
    type: service  # Kubernetes LoadBalancer type (service/ingress), note: only "service" is supported for now
    service:
      type: LoadBalancer
```

### Install Spinnaker Service
Confirm if all the environment variables is set correctly
```
echo $ACCOUNT_ID
echo $AWS_REGION
echo $SPINNAKER_VERSION
echo $GITHUB_USER
echo $GITHUB_TOKEN
echo $S3_BUCKET
echo $AWS_ACCESS_KEY_ID
echo $AWS_SECRET_ACCESS_KEY
echo $ECR_REPOSITORY
```

If you do not see output from the above command for all the Environment Variables, do not proceed to next step
```
cd ~/environment/spinnaker-operator/
envsubst < deploy/spinnaker/basic/spinnakerservice.yml | kubectl -n spinnaker apply -f -
```
```
spinnakerservice.spinnaker.io/spinnaker created
```

It will take some time to bring up all the pods, so wait for few minutes..
```
 # Get all the resources created
kubectl get svc,pod -n spinnaker
```

```
NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)        AGE
service/spin-clouddriver   ClusterIP      10.1x0.xx.71    <none>                                                                    7002/TCP       8d
service/spin-deck          LoadBalancer   10.1x0.yy.xx    ae33c1a7185b14yyyy-1275091989.us-east-2.elb.amazonaws.com   80:32392/TCP   8d
service/spin-echo          ClusterIP      10.1x0.54.127    <none>                                                                    8089/TCP       8d
service/spin-front50       ClusterIP      10.1x0.xx.241   <none>                                                                    8080/TCP       8d
service/spin-gate          LoadBalancer   10.1x0.75.xx    ac3a38db81ebXXXX-1555475316.us-east-2.elb.amazonaws.com   80:32208/TCP   8d
service/spin-igor          ClusterIP      10.1x0.yy.xx    <none>                                                                    8088/TCP       8d
service/spin-orca          ClusterIP      10.xx.64.yy    <none>                                                                    8083/TCP       8d
service/spin-redis         ClusterIP      10.1x0.xx.242    <none>                                                                    6379/TCP       1x0
service/spin-rosco         ClusterIP      10.1x0.yy.xx   <none>                                                                    8087/TCP       8d

NAME                                    READY   STATUS    RESTARTS   AGE
pod/spin-clouddriver-7c5dbf658b-spl64   2/2     Running   0          8d
pod/spin-deck-7f785d675f-2q4q8          1/1     Running   0          8d
pod/spin-echo-d9b7799b4-4wjnn           1/1     Running   0          8d
pod/spin-front50-76d9f8bd58-n96sl       1/1     Running   0          8d
pod/spin-gate-7f48c76b55-bpc22          1/1     Running   0          8d
pod/spin-igor-5c98f5b46f-mcmvs          1/1     Running   0          8d
pod/spin-orca-6bd7c69f-mml4c            1/1     Running   0          8d
pod/spin-redis-7f7d9659bf-whkf7         1/1     Running   0          8d
pod/spin-rosco-7c6f77c64c-2qztw         1/1     Running   0          8d
```
```

# Watch the install progress.
kubectl -n spinnaker get spinsvc spinnaker -w
```
```
NAME        VERSION   LASTCONFIGURED   STATUS   SERVICES   URL
spinnaker   1.24.0    3h8m             OK       9          http://ae33c1a7185b1402mmmmm-1275091989.us-east-2.elb.amazonaws.com
```

### Test the setup on Spinnaker UI
Access Spinakker UI

Grab the load balancer url from the previous step, and load into the browser, you should see the below Spinnaker UI
![Cluster Creation Workflow](imgs/ui.png "Cluster Creation Workflow

### Create a test application
Click on Create Application and enter details.
![Cluster Creation Workflow](imgs/application.png "Cluster Creation Workflow")

### Create a test pipeline
Click on Pipelines under test-application and click on Configure a new pipeline and add the name.
![Cluster Creation Workflow](imgs/pipeline.png "Cluster Creation Workflow")

Click on Add Stage and select Deploy (Manifest) from the dropdown for Type, select spinnaker-workshop from the dropdown for Account and put the below yaml into the Manifest text area and click on “Save Changes”.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
![Cluster Creation Workflow](imgs/manifest1.png "Cluster Creation Workflow")

In the Spinnaker UI, Go to Pipelines and click on Start Manual Execution

![Cluster Creation Workflow](imgs/manual.png "Cluster Creation Workflow")

You will see the pipeline getting triggered and is in progress

![Cluster Creation Workflow](imgs/step2.png "Cluster Creation Workflow")

After few seconds, the pipleine is successful
![Cluster Creation Workflow](imgs/step3.png "Cluster Creation Workflow")

Clicking on execution details you can see the detail of deployment
![Cluster Creation Workflow](imgs/details.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/details1.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/details2.png "Cluster Creation Workflow")

Go to Clusters and verify the deployment
![Cluster Creation Workflow](imgs/deploy.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/deploy2.png "Cluster Creation Workflow")

You can also go to Cloud9 terminal and verify the deployment
```
kubectl get deployment nginx-deployment -n spinnaker

kubectl get pods -l app=nginx -n spinnaker
```

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           108s

NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-6dc677db6-jchq8   1/1     Running   0          3m25s

```

Congratulations! You have successfully installed Spinnaker and created a test pipeline in Spinnaker and deployed the ngnix manifest to EKS cluster.

## Testing Helm-based Pipeline
Now lets deploy Helm Based Application to EKS using Spinnaker pipeline.

### Spinnaker UI
#### Create application
Click on Create Application and enter details as Product-Detail

![Cluster Creation Workflow](imgs/proddetail.png "Cluster Creation Workflow")

#### Create Pipeline
Click on Pipelines under Product-Detail and click on Configure a new pipeline and add the name as below.

![Cluster Creation Workflow](imgs/helmpipeline.png "Cluster Creation Workflow")

#### Setup Trigger
Click on Configuration under Pipelines and click on Add Trigger. This is the ECR registry we had setup in Spinnaker manifest in Configure Artifact module. Follow the below setup and click on “Save Changes”.

![Cluster Creation Workflow](imgs/trigger.png "Cluster Creation Workflow")

#### Setup Bake Stage
- Click on Add Stage and select Bake Manifest for Type from the dropdown

- Select “Helm3” as Render Engine and enter name.

- Select “Define a new artifact” for Expected Artifact and select your Git account that shows in dropdown. This is the Git account we had setup in Spinnaker manifest in Configure Artifact module. And then enter the below git location in the Content URL and add main as the Commit/Branch. This is to provide the the Helm template for the deployment.

https://api.github.com/repos/aws-containers/eks-microservice-demo/contents/spinnaker/proddetail-0.1.0.tgz

- Under the Overrides section, Select “create new artifact” for Expected Artifact and select your Git account that shows in dropdown. This is the Git account we had setup in Spinnaker manifest in Configure Artifact module. And then enter the below git location in the Content URL and add main as the Commit/Branch. This is to provide the overrides for the Helm template using values.yaml.

https://api.github.com/repos/aws-containers/eks-microservice-demo/contents/spinnaker/helm-chart/values.yaml

![Cluster Creation Workflow](imgs/bake.png "Cluster Creation Workflow")

- Edit the Produces Artifact and change the name to helm-produced-artifact and click on Save Changes.
![Cluster Creation Workflow](imgs/bake2.png "Cluster Creation Workflow")

![Cluster Creation Workflow](imgs/bake3.png "Cluster Creation Workflow")

### Setup Deploy Stage
- Click on Add Stage and select Deploy (Manifest) from the dropdown for Type, and give a name as Bake proddetail.
- Select Type as spinnaker-workshop from the dropdown for Account. This is the EKS account we had setup in Spinnaker manifest in Add EKS Account module.
- Select helm-produced-artifact from the dropdown for Manifest Artifact and click on Save Changes.


![Cluster Creation Workflow](imgs/bake3_1.png "Cluster Creation Workflow")

### Test Deployment
Push new container image to ECR for testing trigger
To ensure that the ECR trigger will work in Spinnaker UI:

- First change the content of the file to generate a new docker image digest. ECR trigger in Spinnaker does not work for same docker image digest. Go to ~/environment/eks-microservice-demo/apps/detail/app.js and add a comment to first line of the file like below
// commenting for file for docker image generation
Ensure that the image tag (APP_VERSION) you are adding below does not exist in the ECR repository eks-microservice-demo/test otherwise the trigger will not work. Spinnaker pipeline only triggers when a new version of image is added to ECR.

And then run the below command in Cloud9 terminal.
```
cd ~/environment/eks-microservice-demo
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
export APP_VERSION=1.0
export ECR_REPOSITORY=eks-microservice-demo/test
TARGET=$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPOSITORY:$APP_VERSION
docker build -t $TARGET apps/detail --no-cache
docker push $TARGET
```

Building/Pushing Container images first time to ECR may take around 3-5 minutes

Watch Pipleline getting triggered

![Cluster Creation Workflow](imgs/test.png "Cluster Creation Workflow")

You will see that docker push triggers a deployment in the pipeline.
![Cluster Creation Workflow](imgs/test2.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/test3.png "Cluster Creation Workflow")

Below are the Execution Details of pipeline
![Cluster Creation Workflow](imgs/test4.png "Cluster Creation Workflow")
![Cluster Creation Workflow](imgs/test5.png "Cluster Creation Workflow")

### Get deploymet details
You can see the deployment of frontend and detail service below.
![Cluster Creation Workflow](imgs/hdeploy1.png "Cluster Creation Workflow")

Click on the LoadBalancer link below and paste it on browser like http://a991d7csdsdsdsdsdsds-1949669176.XXXXX.elb.amazonaws.com:9000/
![Cluster Creation Workflow](imgs/hdeploy2.png "Cluster Creation Workflow")

You should see the service up and running as below.
![Cluster Creation Workflow](imgs/hdeploy3.png "Cluster Creation Workflow")


You can also go to Cloud9 terminal and confirm the deployment details
```
	kubectl get all -n detail
	NAME                                   READY   STATUS    RESTARTS   AGE
	pod/frontend-677dd7d654-nzxbq          1/1     Running   0          152m
	pod/nginx-deployment-6dc677db6-jchq8   1/1     Running   0          22h
	pod/proddetail-65cf5d598c-h9l7s        1/1     Running   0          152m

	NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP                                                               PORT(S)          AGE
	service/frontend     LoadBalancer   10.100.73.76    a991d7csdsdsdsdsdsds-1949669176.XXXXX.elb.amazonaws.com   9000:30229/TCP   152m
	service/proddetail   ClusterIP      10.100.37.158   <none>                                                                    3000/TCP         152m

	NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
	deployment.apps/frontend           1/1     1            1           152m
	deployment.apps/nginx-deployment   1/1     1            1           22h
	deployment.apps/proddetail         1/1     1            1           152m

	NAME                                         DESIRED   CURRENT   READY   AGE
	replicaset.apps/frontend-677dd7d654          1         1         1       152m
	replicaset.apps/nginx-deployment-6dc677db6   1         1         1       22h
	replicaset.apps/proddetail-65cf5d598c        1         1         1       152m

```

## cleanup
### Delete Spinnaker artifacts
Namespace deletion may take few minutes, please wait till the process completes.
```
for i in $(kubectl get crd | grep spinnaker | cut -d" " -f1) ; do
kubectl delete crd $i
done

kubectl delete ns spinnaker-operator

kubectl delete ns spinnaker

kubectl delete ns detail
```
### (Optional) Create Old Nodegroup
In case you have existing workloads to evit to this nodegroup before we delete the nodegroup created for this module

Nodegroup creation will take few minutes.
```
eksctl create nodegroup --cluster=eksworkshop-eksctl --name=nodegroup --nodes=3 --node-type=t3.small --enable-ssm --managed
```

### Delete Nodegroup
```
eksctl delete nodegroup --cluster=eksworkshop-eksctl --name=spinnaker
```


# Patching/Upgrading Your EKS Cluster
As EKS tracks upstream Kubernetes that means that customers can, and should, regularly upgrade their EKS so as to stay within the project’s upstream support window. This used to be the current version and two version back (n-2) - but it was [recently extended to three versions back (n-3)](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/).

There is a new major version of Kubernetes every quarter which means that the Kubernetes support window has now gone from three quarters of a year to one full year.

In addition to upgrades to Kuberentes, there are other related upgrades to think about with your cluster as well:

- The Amazon Machine Image (AMI) of your Nodes - including not just the portion of Kubernetes that is part of the image, the kubelet, but everything else there (OS, containerd, etc.). The control plane always supports managing kubelets that are one version behind itself (n-1) to help facilitate this upgrade.
- The foundational DaemonSets that are on deployed onto every EKS cluster (kube-proxy, CoreDNS and the AWS CNI) which may need to be upgraded as you upgrade Kubernetes. [AWS documentation](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html#w665aac14c15b5c17) tells you if this is required and which versions you should upgrade to.
- And any Add-ons/Controllers/Drivers that you’ve added to extend Kubernetes and provide important cluster functionality may need to be upgraded as you upgrade Kuberentes
In this module you’ll follow the AWS suggested process to upgrade your cluster from 1.20 to 1.21 including its Managed Node Group to get first-hand experience with this process and where EKS and Managed Node Groups help.

## The Upgrade Process
The process goes as follows:

- (Optional) Check if the new version you are upgrading to has any API deprecations which will mean that you’ll need to change your YAML Spec files for them to continue to work on the new cluster. This is only the case with some version upgrades such as [1.15 to 1.16](https://kubernetes.io/blog/2019/07/18/api-deprecations-in-1-16/). There are various tools that can help with this such as [kube-no-trouble](https://github.com/doitintl/kube-no-trouble). Since there are not any such deprecations going from 1.20 to 1.21 we’ll skip this step here.
- Run a kubectl get nodes and ensure that all of your Nodes are running the current version. Kubernetes can only support nodes that are one version behind meaning they all need to match the current Kubernetes version so when you upgrade the EKS control plane they’ll then only be one version behind. For Fargate relaunching a Pod (maybe by deleted it and letting the ReplicaSet replace it) will bring it in line with the current Kubernetes version.
- Upgrade the EKS Control Plane to the new major version
- Check if the core add-ons (kubeproxy, CoreDNS and the CNI) that ship with EKS require an upgrade to conincide with the major version upgrade. This will be in our upgrade documentation. If so follow that documentation to upgrade those. In this case (a 1.20 to 1.21 upgrade) the documentation says we’ll need to upgrade both CoreDNS and kubeproxy.
- Upgrade any worker nodes so that the kubelet on them (which will now be one Kubernete major version behind) matches that of the EKS control plane. While you don’t have to do this immediatly it is a good idea to have the Nodes on the same version as the control plane as soon as is practical - plus it will make Step 2 easier the next time you have to upgrade. If you are using Managed Node Groups (as we are here) then EKS can help facilitate this with a safe automated process which orchestrates both the AWS and Kubernetes side of a rolling Node replacement/upgrade. If you are using Fargate then this will happen automatically the next time your Pods are replaced.

## Upgrade EKS COntrol Plane
The first step of this process is to upgrade the EKS Control Plane.

Since we used eksctl to provision our cluster we’ll use that tool to do our upgrade as well.

First we’ll run this command

eksctl upgrade cluster --name=eksworkshop-eksctl
You’ll see in the output that it found our cluster, worked out that it is 1.20 and the next version is 1.21 (you can only go to the next version with EKS) and that everything is ready for us to proceed with an upgrade.
```
eksctl upgrade cluster --name=eksworkshop-eksctl
```
```
[ℹ]  eksctl version 0.66.0
[ℹ]  using region us-west-2
[ℹ]  (plan) would upgrade cluster "eksworkshop-eksctl" control plane from current version "1.20" to "1.21"
[ℹ]  re-building cluster stack "eksctl-eksworkshop-eksctl-cluster"
[✔]  all resources in cluster stack "eksctl-eksworkshop-eksctl-cluster" are up-to-date
[ℹ]  checking security group configuration for all nodegroups
[ℹ]  all nodegroups have up-to-date configuration
[!]  no changes were applied, run again with '--approve' to apply the changes
```

We’ll run it again with an –approve appended to proceed
```
eksctl upgrade cluster --name=eksworkshop-eksctl --approve
```
This process should take approximately 25 minutes. You can continue to use the cluster during the control plane upgrade process but you might experience minor service interruptions. For example, if you attempt to connect to one of the EKS API servers just before or just after it’s terminated and replaced by a new API server running the new version of Kubernetes, you might experience temporary API call errors or connectivity issues. If this happens, retry your API operations until they succeed. Your existing Pods/workloads running in the data plane should not experience any interruption during the control plane upgrade.

Given how long this step will take and that the cluster will continue to work maybe move on to other workshop modules until this process completes then come back to finish once it completes.

## Upgrade EKS COre Add-ons
When you provision an EKS cluster you get three add-ons that run on top of the cluster and that are required for it to function properly:

- kubeproxy
- CoreDNS
- aws-node (AWS CNI or Network Plugin)
Looking at the the [upgrade documentation](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html#w665aac14c15b5c17) for our 1.20 to 1.21 upgrade we see that we’ll need to upgrade the kubeproxy and CoreDNS. In addition to performing these steps manually with kubectl as documented there you’ll find that eksctl can do it for you as well.

Since we are using eksctl in the workshop we’ll run the two necessary commands for it to do these updates for us:
```
eksctl utils update-kube-proxy --cluster=eksworkshop-eksctl --approve
```

and then
```
eksctl utils update-coredns --cluster=eksworkshop-eksctl --approve
```

We can confirm we succeeded by retrieving the versions of each with the commands:
```
kubectl get daemonset kube-proxy --namespace kube-system -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
kubectl describe deployment coredns --namespace kube-system | grep Image | cut -d "/" -f 3
```

## Upgrade Managed Node Group
Finally we have gotten to the last step of the upgrade process which is upgrading our Nodes.

There are two ways to provision and manage your worker nodes - self-managed node groups and managed node groups. In this workshop eksctl was configured to use the managed node groups. This was helpful here as managed node groups make this easier for us by automating both the AWS and the Kubernetes side of the process.

The way that managed node groups does this is:

1. Amazon EKS creates a new Amazon EC2 launch template version for the Auto Scaling group associated with your node group. The new template uses the target AMI for the update.
2. The Auto Scaling group is updated to use the latest launch template with the new AMI.
3. The Auto Scaling group maximum size and desired size are incremented by one up to twice the number of Availability Zones in the Region that the Auto Scaling group is deployed in. This is to ensure that at least one new instance comes up in every Availability Zone in the Region that your node group is deployed in.
4. Amazon EKS checks the nodes in the node group for the eks.amazonaws.com/nodegroup-image label, and applies a eks.amazonaws.com/nodegroup=unschedulable:NoSchedule taint on all of the nodes in the node group that aren’t labeled with the latest AMI ID. This prevents nodes that have already been updated from a previous failed update from being tainted.
5. Amazon EKS randomly selects a node in the node group and evicts all pods from it.
6. After all of the pods are evicted, Amazon EKS cordons the node. This is done so that the service controller doesn’t send any new request to this node and removes this node from its list of healthy, active nodes.
7. Amazon EKS sends a termination request to the Auto Scaling group for the cordoned node.
8. Steps 5-7 are repeated until there are no nodes in the node group that are deployed with the earlier version of the launch template.
9. The Auto Scaling group maximum size and desired size are decremented by 1 to return to your pre-update values.

If we instead had used a self-managed node group then we need to do the Kubernetes taint and draining steps ourselves to ensure Kubernetes knows that Node is going away and can manage that process gracefully in order for such an upgrade to be non-disruptive.

The first step only applies to if we are using the cluster autoscaler. We don’t want conflicting Node scaling actions during our upgrade so we should scale that to zero to suspend it during this process using the command below. Unless you have done that module in the workshop and left it deployed you can skip this step.
```
kubectl scale deployments/cluster-autoscaler --replicas=0 -n kube-system
```

We can then trigger the MNG upgrade process by running the following eksctl command:
```
eksctl upgrade nodegroup --name=nodegroup --cluster=eksworkshop-eksctl --kubernetes-version=1.21
```

In another Terminal tab you can follow the progress with:
```
kubectl get nodes --watch
```

You’ll notice the new nodes come up (three one in each AZ), one of the older nodes go STATUS SchedulingDisabled, then eventually that node go away and another new node come up to replace it and so on as described in the process above until all the old Nodes have gone away. Then it’ll scale back down from 6 Nodes to the original 3.
# Conclusion
## What we've accomplished?
We have:

- Deployed an application consisting of microservices
- Deployed the Kubernetes Dashboard
- Deployed packages using Helm
- Configured Automatic scaling of our pods and worker nodes

# Final cleanup
## Undeploy the applications
To delete the resources created by the applications, we should delete the application deployments and kubernetes dashboard.

Note that if you followed the cleanup section of the modules, some of the commands below might fail because there is nothing to delete and its ok.

Undeploy the applications:
```
cd ~/environment/ecsdemo-frontend
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-crystal
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-nodejs
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

export DASHBOARD_VERSION="v2.0.0"

kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/src/deploy/recommended/kubernetes-dashboard.yaml
```

## Delete the EKSCTL Cluster
In order to delete the resources created for this EKS cluster, run the following commands:

Delete the cluster:
```
eksctl delete cluster --name=eksworkshop-eksctl
```

Without the --wait flag, this will only issue a delete operation to the cluster’s CloudFormation stack and won’t wait for its deletion. The nodegroup will have to complete the deletion process before the EKS cluster can be deleted. The total process will take approximately 15 minutes, and can be monitored via the CloudFormation [Console](https://console.aws.amazon.com/cloudformation/home).


## Cleanup the Workspace
Since we no longer need the Cloud9 instance to have Administrator access to our account, we can delete the workspace we created:

- Go to your [Cloud9 Environment](https://console.aws.amazon.com/cloud9/home)
- Select the environment named `eksworkshop` and pick `delete`

# Useful Resources
- [EKS Anywhere](https://anywhere.eks.amazonaws.com/docs/getting-started/)
- [Containers from the couch](https://containersfromthecouch.com/)

## Tools for AWS Fargate and Amazon ECS
- [Containers on AWS](https://containersonaws.com/) - Learn common best-practices for running containers on AWS
- [fargate](http://somanymachines.com/fargate/) - Command line tool for interacting with AWS Fargate. With just a single command you can build, push, and launch your container in Fargate, orchestrated by ECS.
- [Wonqa](https://www.npmjs.com/package/wonqa) is a tool for spinning up disposable QA environments in AWS Fargate, with SSL enabled by Let’s Encrypt. More details about Wonqa on the [Wonder Engineering blog](https://medium.com/wonder-engineering/on-demand-qa-environments-with-aws-fargate-c23b41f15a0c)
- [coldbrew](https://github.com/coldbrewcloud/coldbrew-cli) - Fantastic tool that provisions ECS infrastructure, builds and deploys your container, and connects your services to an application load balancer automatically. Has a great developer experience for day to day use
- [mu](https://github.com/stelligent/mu) - Automates everything relating to ECS devops and CI/CD. This framework lets you write a simple metadata file and it constructs all the infrastructure you need so that you can deploy to ECS by simply pushing to your Git repo.

# What to learn next?
- Spinnaker for Continuous Delivery
- Amazon ECS
- EKS Networking
- Amazon App Mesh
