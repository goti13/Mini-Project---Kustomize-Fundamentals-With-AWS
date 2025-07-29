# Mini-Project---Kustomize-Fundamentals-With-AWS

Lesson 2.1: Kustomize Structure and Concepts

Objective: Understand the directory structure, files, and key concepts of Kustomize.

Tasks and Detailed Steps:

1. Understanding Kustomize Structure:

- Base Directory: Contains the common, default resources for your application.

- Overlays Directory: Contains environment-specific variations.

- Kustomization File: 'kustomization.yaml', used to declare the resources, bases, and overlays.

2. Key Concepts:

- Resources: Kubernetes object manifests (e.g., deployments, services).

- Bases: Starting point of a configuration, often the generic version.

- Overlays: Modifications or environment-specific settings applied to the base.

3. Activity:

- Create a basic directory structure:


```
myapp/
├── base/
│   └── deployment.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
```

- Populate 'deployment, yaml with a simple deployment manifest.

Lesson 2.2: Creating and Managing Resources

Objective: Learn how to define and manage resources in Kustomize.

Tasks and Detailed Steps:
1. Define a Basic Deployment in Base:

- In ' base/deployment.yaml', define a basic deployment (e.g., a simple NGINX server).


- Sample deployment:

```

apiVersion: apps/v1    # API version for the deployment object
kind: Deployment       # Specifies that this is a Deployment
metadata:
  name: nginx-deployment  # Name of the deployment
spec:                   # Specification of the deployment
  replicas: 2           # Number of replicas (pods running)
  selector:             # Selector to identify the pods
    matchLabels:
      app: nginx
  template:             # Template for the pod creation
    metadata:
      labels:
        app: nginx     # Label applied to the pod
    spec:
      containers:      
        - name: nginx    # Name of the container
          image: nginx:1.14.2  # Docker image to use for the container
          ports:
            - containerPort: 80  # Port the container will expose


```

2. Include the Deployment in kustomization.yaml:

-  In ' base/kustomization.yaml', reference the deployment:

```
resources:
  - deployment.yaml # List of resource files to include in this configuration

```

3. Managing Kubernetes Objects:

- Understand how to create and manage other common Kubernetes objects like services and pods using similar steps.

Lesson 2.3: Basic Customization Techniques

Objective: Learn to customize resource configurations using Kustomize.

Tasks and Detailed Steps:

1. Creating Overlays:

- Create two overlays: 'dev' and 'prod' in respective directories.
  
In 'overlays/dev/kustomization.yaml'
, reference the base and make a simple change like increasing the number of replicas.

2. Customization Example:

- Dev overlay (' overlays/dev/kustomization.yaml'):

```

bases:
  - ../../base # Path to the base configuration directory
patchesStrategicMerge:
  - replica_count.yaml # List of patches to apply to the base resources

```

In 'overlays/dev/replica_count.yaml'  define the patch:


```

apiVersion: apps/v1 # API version for the deployment object
kind: Deployment # Specifies that this is a Deployment
metadata:
  name: nginx-deployment # Name of the deployment to patch
spec:
  replicas: 3  # Updating the number of replicas for the dev environment

```

3. Using Variables and Placeholders:

- Discuss how Kustomize allows the use of variables and placeholders for dynamic configuration values (refer to the [Kustomize documentation](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/vars/)).


**Leveraging AWS**


Expanding on leveraging AWS, specifically Amazon EKS (Elastic Kubernetes Service), for deploying and testing your Kustomize configurations requires a detailed step-by-step approach. This guide is designed for beginners and will walk you through the process from creating an EKS cluster to deploying your configurations.


Leveraging AWS: Using Amazon EKS with Kustomize

Step 1: Set Up Your AWS Account and CLI

1. ﻿﻿﻿Create an AWS Account: If you don't already have an AWS account, create one at [AWS Management Console](https://aws.amazon.com/console/).

2. Install AWS CLI:

- Download and install the AWS CLI from the [official guide](https://aws.amazon.com/cli/).

- Configure the CLI with your credentials: Run 'aws configure' and enter your AWS Access Key ID, Secret Access Key, and default region.

Step 2: Install and Configure 'eksctl

'eksctl' is a simple CLI tool for creating clusters on EKS. It's the easiest way to get started with Amazon EKS.

1. Install 'eksctl':

- Follow the installation instructions on the [‘eksctl’](https://github.com/eksctl-io/eksctl) GitHub repository.

2. Configure 'eksctl':

- No specific configuration is required post-installation. It works with your AWS CLI configuration.

Step 3: Create an EKS Cluster

1. Create a Cluster:

Run the following command to create a cluster (this can take several minutes):

```

eksctl create cluster --name my-kustomize-cluster --version 1.33 --region us-west-2 --nodegroup-name my-nodes --node-type t2.medium --nodes 3

```

<img width="2058" height="1202" alt="image" src="https://github.com/user-attachments/assets/c944f7c3-99be-4c0d-954e-c1c71987c9f9" />


<img width="2044" height="1048" alt="image" src="https://github.com/user-attachments/assets/1fe0bf82-67cd-445c-a746-d4d8ec519f43" />


<img width="1676" height="258" alt="image" src="https://github.com/user-attachments/assets/25e39a75-cb3b-4d12-845b-7fbb06a706a2" />


<img width="2088" height="484" alt="image" src="https://github.com/user-attachments/assets/a76e746b-e71d-4d66-8970-2e408a06b8ac" />



This command creates an EKS cluster named 'my-kustomize-cluster' with 3 nodes of type ' t2 medium'.


2. Verify Cluster Creation:

- Once the creation process is complete, verify your cluster with: 'kubectl get svc'.

Step 4: Deploying Kustomize Configurations to EKS

1. Prepare Your Kustomize Configuration:

- Ensure your Kustomize configuration is ready in your project directory. For instance, you should have your 'base' and 'overlays' directories set up as per previous lessons.

2. Apply Configuration to EKS:

- Select the appropriate overlay for your deployment environment. For example, if deploying to a development environment, you might choose the 'dev' overlay.

- Run the following command from the root of your project directory:


```
kubectl apply -k overlays/dev/

```

This applies the configurations defined in your dev' overlay to the EKS cluster.
Step 5: Verify Deployment

1. Check Deployed Resources:
- Run 'kubectl get all' to see all the resources (like pods, services, deployments) that have been deployed to your cluster.
- You should see the resources defined in your Kustomize configurations.

<img width="2072" height="694" alt="image" src="https://github.com/user-attachments/assets/30558840-c158-4c48-8ce6-65288c22aa09" />


2. Troubleshoot if Needed:
-  If resources are not appearing as expected, use 'kubectl describe' or 'kubectl logs' commands to troubleshoot.

Step 6: Clean Up Resources

1. Delete Deployed Resources:

- When you're done with testing, clean up your resources to avoid incurring unnecessary costs.
- Run 'kubectl delete -k overlays/dev/ to remove the resources.

<img width="1758" height="66" alt="image" src="https://github.com/user-attachments/assets/eb8ab2a8-c286-4d66-befe-4f5ad80fcc36" />


2. Delete the EKS Cluster:

- Remove your EKS cluster with: 'eksctl delete cluster --name my-kustomize-cluster'

<img width="1954" height="430" alt="image" src="https://github.com/user-attachments/assets/3b6b84ce-3f96-48f2-b703-e3fa877c1217" />

Additional Resources and Considerations
- AWS Costs: Be mindful of AWS costs associated with EKS. Always clean up resources after use.

- EKS Documentation: Refer to the [EKS User Guide](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html) for more detailed information.

- Security Practices: Follow best practices for AWS security, like using lAM roles and security groups effectively.


Conclusion:

This project provides a foundational understanding of Kustomize, its structure, and basic techniques for customizing Kubernetes resources. By implementing these in an AWS environment,
learners gain practical skills that are highly relevant in modern cloud-native ecosystems.
