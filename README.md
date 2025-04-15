# Demo Webapp

This repository contains the infrastructure and deployment configuration for a simple web application. The infrastructure is managed using Terraform, and the application is deployed to an Amazon EKS cluster using Helm.

## Prerequisites

Before you begin, ensure you have the following installed on your local machine:
- [Terraform](https://www.terraform.io/downloads.html)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [AWS CLI](https://aws.amazon.com/cli/)
- [Helm](https://helm.sh/docs/intro/install/)

## Setting Up AWS Credentials
You can install the required dependencies using Homebrew. Run the following command to install all dependencies at once:

```bash
brew install terraform kubectl awscli helm
```

Ensure that Homebrew is installed on your system. If not, you can install it by following the instructions at [https://brew.sh/](https://brew.sh/).
To run Terraform commands, you need to set your AWS access key and secret key as environment variables. Use the following commands:

```bash
export AWS_ACCESS_KEY_ID=<your-access-key-id>
export AWS_SECRET_ACCESS_KEY=<your-secret-access-key>
```

Replace `<your-access-key-id>` and `<your-secret-access-key>` with your actual AWS credentials.

## Running Terraform

1. Initialize Terraform:
    ```bash
    terraform init
    ```

2. Plan the infrastructure changes:
    ```bash
    terraform plan
    ```

3. Apply the changes to create the infrastructure:
    ```bash
    terraform apply
    ```

    After a successful `terraform apply`, Terraform will output the necessary details for configuring your Kubernetes context.

## Configuring Kubernetes Context

Once the EKS cluster is created, configure your `kubectl` context to interact with the cluster:

1. Retrieve the cluster name and region from the Terraform output.
2. Run the following command to update your kubeconfig:
    ```bash
    aws eks --region <region> update-kubeconfig --name <cluster-name>
    ```

    Replace `<region>` and `<cluster-name>` with the appropriate values.

3. Verify the configuration:
    ```bash
    kubectl get nodes
    ```

    You should see the nodes in your EKS cluster.

## Set up Nginx for Ingress

Before deploying the web application, you need to install and configure NGINX as an Ingress Controller. Follow these steps:

1. Add the NGINX Helm repository:
    ```bash
    helm repo add nginx-stable https://helm.nginx.com/stable
    helm repo update
    ```

2. Install the NGINX Ingress Controller:
    ```bash
    helm install nginx-ingress nginx-stable/nginx-ingress
    ```

3. Verify the installation:
    ```bash
    kubectl get pods -n default -l app.kubernetes.io/name=nginx-ingress
    ```

    Ensure the NGINX Ingress Controller pods are running successfully.

Once NGINX is installed and running, you can proceed with deploying the web application.
Ensure the NGINX Ingress Controller pods are running successfully.

## Deploying the Application

The application is deployed using Helm and the configuration files located in `kubernetes/simple-web-app`.

1. Navigate to the `kubernetes/simple-web-app` directory:
    ```bash
    cd kubernetes/simple-web-app
    ```

2. Deploy the application using Helm:
    ```bash
    helm install simple-web-app .
    ```

## Accessing the web application
```bash
    kubectl get svc -o jsonpath='{.items[?(@.spec.type=="LoadBalancer")].status.loadBalancer.ingress[0].hostname}'
    ```

This will return the default DNS name for the load balancer, which can be pasted into your browser to hit the web app.

## Cleaning Up

To destroy the infrastructure and clean up resources, run:
```bash
terraform destroy
```

## Additional Notes

- Ensure your AWS CLI is configured with the correct region and credentials.
- Modify the Helm chart values in `kubernetes/simple-web-app/values.yaml` as needed for your application.

For any issues or questions, feel free to open an issue in this repository.