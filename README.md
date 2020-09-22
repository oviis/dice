# dice
Demo of k8s setup on AWS with Istio and Spinnaker

## Prerequisites

### Docker
The installation instructions assume that you have a working installation of Docker. If you want to run without Docker, you will have to make sure to use compatible versions of any required third-party tools, including:

* kubectl
* kops
* helm
* jq
* halyard
* istioctl

Verify your Docker installation:

	$ docker run alpine /bin/echo 'Good to go!'
	Good to go!

### AWS
You will also need an AWS account with a user who can create and manage policies, roles and other users. IAMFullAccess should be sufficient, but more restrictive policies may work as well.

We need to configure a profile named `dice` for this user in AWS CLI:

	$ aws configure --profile dice
	# enter your credentials and region, then verify like this:

	$ aws --profile dice sts get-caller-identity
	{
		"UserId": "AIDA...",
		"Account": "...",
		"Arn": "arn:aws:iam::...:user/..."
	}

Your account should have a Hosted Zone in Route 53 for the domain in which you want to host the k8s cluster. E.g. if your cluster is to be called `dice.example.com`, you should have a Route 53 Hosted Zone for `example.com`. Verify your DNS setup:

	$ aws route53 list-hosted-zones | jq '.HostedZones[] | select(.Name == "example.com.") | .Id'  # note the extra dot at the end of the domain name
	"/hostedzone/..."

	$ dig +short NS example.com
	ns-1.awsdns-1.org.
	ns-2.awsdns-2.net.
	ns-3.awsdns-3.com.
	ns-4.awsdns-4.co.uk.

## Installation

Adjust the values in [config.env](config.env) to your environment and run

	$ make cluster