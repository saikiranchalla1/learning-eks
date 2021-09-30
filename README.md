# Amazon EKS
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
  ![Popout](imgs/[popout].png "K8s Dashboard")

- Open a New Terminal Tab and enter
```
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```

- Copy the output of this command and then click the radio button next to Token then in the text field below paste the output from the last command.
  ![Popout](imgs/[dashboard-connect].png "K8s Dashboard")

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

You should also be able to copy/paste the loadBalancer hostname into your browser and see the application running. Keep this tab open while we scale the services up on the next page.

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

### Clean Up
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
