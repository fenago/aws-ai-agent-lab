# Lab Guide: Building an AWS AI Agent Foundation with Free Tier Services

**Author**: Dr. Ernesto Lee, CEO LVNG.ai, DrLee.ai  
**Date**: February 26, 2026

---

## Introduction

This comprehensive lab guide provides a step-by-step walkthrough for setting up a foundational environment on Amazon Web Services (AWS) to build and operate an AI agent. We will leverage the **AWS Free Tier** to minimize costs while exploring powerful cloud capabilities. The lab covers creating an AWS account, launching virtual machines (EC2 instances) via both the graphical interface and command line, deploying a containerized application with Docker, configuring network security, and finally, setting up a **Model Context Protocol (MCP) server** to enable your AI agent to interact with AWS services programmatically.

### Lab Objectives

By completing this lab, you will:

*   Understand and effectively utilize the AWS Free Tier, including the distinction between 12-month free and always-free services.
*   Create and configure a new AWS account.
*   Launch an EC2 virtual machine using the AWS Management Console.
*   Launch an EC2 virtual machine using the AWS Command Line Interface (CLI).
*   Connect to your EC2 instance via SSH (local terminal) and via browser (EC2 Instance Connect).
*   Install and configure Docker on an EC2 instance.
*   Pull and run public container images from Docker Hub (featuring OpenClaw).
*   Create, containerize, and deploy a simple web application.
*   Configure AWS Security Groups to control network access (firewall rules).
*   Deploy a serverless Model Context Protocol (MCP) server using AWS Lambda and API Gateway.
*   Add custom AWS tools to your MCP server to enable AI agent automation.
*   Connect an AI agent (such as Claude Code) to your MCP server and test its capabilities.

---

## Part 1: AWS Account Setup & EC2 Instance Creation

### 1.1 Understanding the AWS Free Tier

Before we begin, it is crucial to understand the AWS Free Tier. It comes in two main types: **12-Month Free** and **Always Free**. The 12-month free tier starts when you create your account and offers more generous usage limits for specific services, while the Always Free tier has lower limits but does not expire [1].

**AWS Free Tier Types:**

| Tier Type | Duration | Description |
| :--- | :--- | :--- |
| **12-Month Free** | 12 months from sign-up | Provides a generous allocation for services like EC2, S3, and RDS. Ideal for getting started and learning the platform. **After 12 months, you pay standard rates.** |
| **Always Free** | Does not expire | Offers a smaller, indefinite free usage allowance for select services like AWS Lambda (1 million requests/month), DynamoDB (25 GB storage), and SNS (1 million publishes/month). **These services are free forever, up to their specified limits.** |

For this lab, we will primarily use services under the **12-Month Free** tier, specifically Amazon EC2. It is important to note that the EC2 free tier allocation **expires after 12 months**. We will also use AWS Lambda and API Gateway, which fall under the **Always Free** tier for the usage levels in this lab.

**Key Free Tier Limits for EC2 (as of Feb 2026):**

| Service | Free Tier Allocation (Monthly) | Tier Type | Details |
| :--- | :--- | :--- | :--- |
| **EC2 Instances** | 750 hours | 12-Month Free | of `t2.micro` or `t3.micro` instance type (depending on region) [3]. This is enough to run one instance 24/7. |
| **Public IPv4 Address** | 750 hours | 12-Month Free | of usage per month, aligned with your EC2 instance hours [12]. |
| **EBS Storage** | 30 GB | 12-Month Free | of General Purpose (SSD) or Magnetic storage. |
| **Data Transfer** | 100 GB | 12-Month Free | of data transfer out from AWS to the internet. |
| **AWS Lambda** | 1 million requests | Always Free | Plus 400,000 GB-seconds of compute time per month. |
| **API Gateway** | 1 million API calls | 12-Month Free | For REST APIs. |

> **Important**: The 12-Month Free Tier lasts for 12 months from the date you create your account. Exceeding the usage limits will result in standard pay-as-you-go charges. Always monitor your usage via the AWS Billing Dashboard to avoid unexpected costs [2].

### 1.2 Creating Your AWS Account

Follow these steps to create your free AWS account.

1.  **Navigate to the AWS Free Tier page**: Open your web browser and go to [https://aws.amazon.com/free/](https://aws.amazon.com/free/).
2.  **Create an Account**: Click the "Create a Free Account" button.
3.  **Provide Account Information**: You will be asked for an email address, a password, and an AWS account name. Choose these carefully and click "Continue".
4.  **Contact Information**: Select "Personal" as the account type and fill in your personal details. You will need to provide a valid phone number and address.
5.  **Billing Information**: You must provide a valid credit or debit card. AWS will make a small temporary charge (typically $1) to verify the card, which will be refunded. This card will be charged for any usage that exceeds the Free Tier limits.
6.  **Identity Verification**: AWS will verify your identity via a text message (SMS) or an automated phone call. Enter the verification code you receive.
7.  **Choose a Support Plan**: Select the "Basic Support - Free" plan.
8.  **Complete Sign-up**: Once you complete the sign-up, your account will be activated. This can take a few minutes to a few hours. You will receive a confirmation email once your account is ready.

### 1.3 Method 1: Launching an EC2 Instance via the AWS Management Console

The AWS Management Console is a web-based interface for managing your AWS services. This is the most straightforward way to launch your first virtual machine.

1.  **Sign In**: Log in to the [AWS Management Console](https://aws.amazon.com/console/).
2.  **Navigate to EC2**: In the search bar at the top, type "EC2" and select "EC2" from the results.
3.  **Launch Instance**: Click the orange "Launch instance" button.
4.  **Name and Tags**: Give your instance a descriptive name, for example, `my-ai-agent-server`.
5.  **Application and OS Images (AMI)**: Select an Amazon Machine Image (AMI). For this lab, choose **Ubuntu**. Select the latest **Ubuntu Server LTS** version (e.g., Ubuntu Server 22.04 LTS) that is marked as "Free tier eligible".
6.  **Instance Type**: Choose the `t2.micro` instance type, which is marked as "Free tier eligible". This instance type provides 1 vCPU and 1 GB of memory.
7.  **Key Pair (for login)**: This is critical for accessing your instance securely via SSH.
    *   Click "Create new key pair".
    *   Enter a key pair name (e.g., `my-aws-key`).
    *   Choose **RSA** for the key pair type.
    *   Choose **.pem** for the private key file format (for use with OpenSSH).
    *   Click "Create key pair". Your browser will download the `.pem` file. **Store this file securely; you cannot download it again. If you lose it, you will not be able to access your instance.**
8.  **Network Settings (Security Group)**:
    *   Click "Edit" in the Network settings panel.
    *   In the "Security group rule 1" section, ensure the type is **SSH** (port 22).
    *   For the "Source type", select **My IP**. This will automatically populate your current public IP address, restricting SSH access to your machine only. This is a critical security measure to prevent unauthorized access.
    *   We will add more rules later for our web application.
9.  **Configure Storage**: The default 8 GB of `gp2` (General Purpose SSD) storage is within the Free Tier limit (30 GB). You can increase this to 30 GB if needed, but 8 GB is sufficient for this lab.
10. **Advanced Details**: You can leave the advanced details at their default settings for this lab.
11. **Summary**: Review the summary of your instance configuration on the right side of the screen.
12. **Launch Instance**: Click the orange "Launch instance" button.

It will take a few minutes for your instance to launch. You can view its status in the EC2 Instances dashboard. Wait until the "Instance state" shows as "Running" and the "Status check" shows "2/2 checks passed".

### 1.4 Method 1: Connecting to Your EC2 Instance via SSH (Local Terminal)

This method uses your local computer's terminal and the `.pem` key file you downloaded. It is a standard practice for developers.

1.  **Open a Terminal**: On your local machine, open a terminal or command prompt.
2.  **Locate Your Key Pair**: Navigate to the directory where you saved your `.pem` file (e.g., your Downloads folder).
3.  **Set Permissions**: You must restrict the permissions of your key file so that only you can read it. This is a security requirement for SSH. Run the following command:
    ```shell
    chmod 400 my-aws-key.pem
    ```
4.  **Get Public IP Address**: In the EC2 dashboard, select your instance and find the "Public IPv4 address" in the details pane below.
5.  **Connect via SSH**: Use the following command, replacing `your-public-ip` with the address you just copied and `my-aws-key.pem` with your key file name:
    ```shell
    ssh -i "my-aws-key.pem" ubuntu@your-public-ip
    ```
6.  **Confirm Connection**: The first time you connect to a new server, you will be asked to confirm the host authenticity. Type `yes` and press Enter.

You are now connected to your EC2 instance! You should see a command prompt that looks like `ubuntu@ip-xxx-xxx-xxx-xxx:~$`.

### 1.5 Method 2: Connecting via Browser (EC2 Instance Connect)

For a simpler approach that doesn't require a local terminal or managing `.pem` files, you can connect directly from your web browser.

1.  **Navigate to EC2 Instances**: In the AWS Management Console, go to the EC2 dashboard and select "Instances".
2.  **Select Your Instance**: Check the box next to the instance you want to connect to.
3.  **Click Connect**: Click the "Connect" button at the top of the dashboard.
4.  **Choose Connection Method**: Select the "EC2 Instance Connect" option. The User name will be pre-filled as `ubuntu`.
5.  **Connect**: Click the orange "Connect" button.

A new browser tab will open with a fully functional terminal session connected to your EC2 instance. This is the easiest way to get started.

### 1.6 Method 3: Launching an EC2 Instance via the AWS CLI

For automation, scripting, and advanced workflows, the AWS Command Line Interface (CLI) is essential. First, you need to install and configure it on your local machine.

#### 1.6.1 Installing and Configuring the AWS CLI

1.  **Install the AWS CLI**: Follow the official instructions for your operating system: [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html). For most systems, you can use a package manager or download an installer.

2.  **Create IAM User for Programmatic Access**:
    *   In the AWS Console, navigate to **IAM** (Identity and Access Management) by typing "IAM" in the search bar.
    *   Go to **Users** in the left-hand menu and click "Add users".
    *   Enter a user name (e.g., `cli-admin`).
    *   For "Select AWS access type", check **Access key - Programmatic access**.
    *   Click "Next: Permissions".
    *   Select "Attach existing policies directly".
    *   In the search box, type "AdministratorAccess" and check the box next to the `AdministratorAccess` policy. **Note**: For production environments, you should use more restrictive, least-privilege policies. For this lab, administrator access is sufficient for learning purposes.
    *   Click "Next: Tags" (you can skip adding tags).
    *   Click "Next: Review".
    *   Click "Create user".
    *   **IMPORTANT**: On the final screen, you will see an **Access key ID** and a **Secret access key**. Click "Show" to reveal the secret access key. **Copy both of these immediately and store them securely (e.g., in a password manager). You will not be able to see the secret key again.** If you lose it, you will need to create a new access key.

3.  **Configure the CLI**: Open your terminal and run:
    ```shell
    aws configure
    ```
    *   **AWS Access Key ID**: Paste the Access Key ID you copied.
    *   **AWS Secret Access Key**: Paste the Secret Access Key you copied.
    *   **Default region name**: Enter your desired AWS region (e.g., `us-east-1`). Choose a region geographically close to you for lower latency.
    *   **Default output format**: Enter `json`.

#### 1.6.2 Launching an EC2 Instance with the CLI

Now you are ready to launch an instance via the CLI. The process requires you to find the AMI ID and create a Security Group first.

1.  **Find the AMI ID**: You can find the ID of the latest Ubuntu AMI by using the AWS CLI. Run the following command (this example is for `us-east-1` region and Ubuntu 22.04 LTS):
    ```shell
    aws ec2 describe-images --owners 099720109477 --filters "Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*" --query 'sort_by(Images, &CreationDate)[-1].ImageId' --output text
    ```
    This will output the AMI ID (e.g., `ami-0c55b159cbfafe1f0`). Copy this ID.

2.  **Create a Security Group**: We will create a new security group for our CLI-launched instance.
    ```shell
    aws ec2 create-security-group --group-name cli-sg --description "Security group for CLI instance"
    ```
    Note the `GroupId` from the output (e.g., `sg-0123456789abcdef0`). Copy this ID.

3.  **Add a Firewall Rule for SSH**: Allow SSH access from your current IP address. First, get your public IP:
    ```shell
    MY_IP=$(curl -s http://checkip.amazonaws.com)
    echo "Your IP: $MY_IP"
    ```
    Then, add the SSH rule to the security group, replacing `sg-0123456789abcdef0` with your actual security group ID:
    ```shell
    aws ec2 authorize-security-group-ingress --group-id sg-0123456789abcdef0 --protocol tcp --port 22 --cidr $MY_IP/32
    ```

4.  **Launch the EC2 Instance**: Now, launch the instance using the AMI ID, instance type, key pair name (the one you created in the Console method), and security group ID. Replace the placeholders with your actual values:
    ```shell
    aws ec2 run-instances \
      --image-id ami-0c55b159cbfafe1f0 \
      --count 1 \
      --instance-type t2.micro \
      --key-name my-aws-key \
      --security-group-ids sg-0123456789abcdef0 \
      --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=my-cli-agent-server}]'
    ```

This command will output a JSON object with the details of your newly launching instance. Look for the `InstanceId` field (e.g., `i-0123456789abcdef0`).

5.  **Get the Public IP**: Once the instance is running, you can get its public IP address with the following command, replacing `i-0123456789abcdef0` with your instance ID:
    ```shell
    aws ec2 describe-instances --instance-ids i-0123456789abcdef0 --query 'Reservations[0].Instances[0].PublicIpAddress' --output text
    ```

You can now connect to this instance using the same SSH method as before, using the public IP address you just retrieved.

---

## Part 2: Docker Container Deployment & Firewall Configuration

This section guides you through installing Docker on your EC2 instance, creating a simple web application, containerizing it with Docker, and configuring the AWS security group to allow public access.

### 2.8 Highlight: Pulling and Running Public Containers from Docker Hub

Docker Hub is a public registry of millions of container images. You can pull and run any public image with a single command, which is a powerful way to deploy pre-built applications. As a highlight, we will pull and run the **OpenClaw** container, an open-source AI assistant.

1.  **Search for an Image**: You can search for images on the [Docker Hub website](https://hub.docker.com/). We will use the `1panel/openclaw` image.

2.  **Pull the Image**: Use the `docker pull` command to download the image to your EC2 instance.
    ```shell
    docker pull 1panel/openclaw
    ```
    This will download the OpenClaw image and all its layers.

3.  **Run the Container**: Run the OpenClaw container. We will map port 3000 on the EC2 instance to port 80 inside the container.
    ```shell
    docker run -d -p 3000:80 --name openclaw 1panel/openclaw
    ```

4.  **Update Security Group**: You need to open port 3000 in your security group to access the OpenClaw web interface.
    *   Go to your Security Group in the AWS Console.
    *   Click "Edit inbound rules".
    *   Click "Add rule".
    *   For "Type", select **Custom TCP**.
    *   For "Port range", enter `3000`.
    *   For "Source", select **Anywhere-IPv4** (`0.0.0.0/0`).
    *   Click "Save rules".

5.  **Access OpenClaw**: Open your web browser and navigate to `http://your-public-ip:3000`. You should see the OpenClaw setup screen.

This demonstrates how easily you can deploy complex applications like OpenClaw just by pulling a pre-built image from Docker Hub.

### 2.1 Installing Docker on the EC2 Instance

First, connect to the EC2 instance you created in Part 1 using SSH (either the Console-launched or CLI-launched instance).

1.  **Update Package Index**: Ensure your package manager has the latest list of available software packages.
    ```shell
    sudo apt-get update
    ```

2.  **Install Docker**: Install the Docker engine.
    ```shell
    sudo apt-get install -y docker.io
    ```

3.  **Start and Enable Docker**: Ensure the Docker service starts automatically on boot.
    ```shell
    sudo systemctl start docker
    sudo systemctl enable docker
    ```

4.  **Verify Docker Installation**: Check that Docker is running correctly.
    ```shell
    sudo docker --version
    ```
    You should see output like `Docker version 20.10.x, build xxxxxxx`.

5.  **Add `ubuntu` User to Docker Group**: To run Docker commands without needing `sudo` every time, add your user to the `docker` group. **You will need to log out and log back in for this change to take effect.**
    ```shell
    sudo usermod -aG docker ubuntu
    ```
    After running this command, exit your SSH session by typing `exit` and then reconnect using the same SSH command as before.

6.  **Verify Group Membership**: After reconnecting, verify that you can run Docker without `sudo`:
    ```shell
    docker ps
    ```
    This command should run without errors (it will likely show an empty list of containers, which is expected).

### 2.2 Creating a Simple Web Application

We will create a basic Python web application using the Flask framework.

1.  **Install Python and Pip**: Ubuntu 22.04 comes with Python 3 pre-installed, but we need `pip` to install Python packages.
    ```shell
    sudo apt-get install -y python3-pip
    ```

2.  **Install Flask**: Install the Flask web framework.
    ```shell
    pip3 install Flask
    ```

3.  **Create Application Directory**: Create a directory for your web application.
    ```shell
    mkdir ~/my-web-app
    cd ~/my-web-app
    ```

4.  **Create the Python App**: Create a file named `app.py` using the `nano` text editor.
    ```shell
    nano app.py
    ```
    Paste the following code into the editor:
    ```python
    from flask import Flask
    import os

    app = Flask(__name__)

    @app.route('/')
    def hello():
        hostname = os.uname()[1]
        return f"<h1>Hello from your Docker container!</h1><p>Container Hostname: <strong>{hostname}</strong></p>"

    if __name__ == '__main__':
        app.run(debug=True, host='0.0.0.0', port=5000)
    ```
    Save and exit the editor by pressing `Ctrl+X`, then `Y` (for yes), then `Enter`.

This simple Flask application listens on port 5000 and returns a greeting message along with the container's hostname.

### 2.3 Containerizing the Application with Docker

Now, we will create a `Dockerfile` to package our application into a Docker image.

1.  **Create the Dockerfile**: In the `my-web-app` directory, create a file named `Dockerfile` (no file extension).
    ```shell
    nano Dockerfile
    ```

2.  **Add Dockerfile Instructions**: Paste the following into the editor:
    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.8-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Make port 5000 available to the world outside this container
    EXPOSE 5000

    # Define environment variable
    ENV NAME World

    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```
    Save and exit (`Ctrl+X`, `Y`, `Enter`).

3.  **Create a `requirements.txt` file**: The Dockerfile references this file to install Python dependencies.
    ```shell
    nano requirements.txt
    ```
    Add the following line:
    ```
    Flask
    ```
    Save and exit.

### 2.4 Building and Running the Docker Container

1.  **Build the Docker Image**: From within the `~/my-web-app` directory, build the Docker image. The `-t` flag tags the image with a name.
    ```shell
    docker build -t my-flask-app .
    ```
    This command will download the base Python image, copy your application files, and install Flask. It may take a minute or two.

2.  **Verify the Image**: List your Docker images to confirm the build was successful.
    ```shell
    docker images
    ```
    You should see `my-flask-app` in the list.

3.  **Run the Docker Container**: Run the container, mapping port 80 on the EC2 instance to port 5000 inside the container. The `-d` flag runs the container in detached mode (in the background), and `-p` maps the ports.
    ```shell
    docker run -d -p 80:5000 my-flask-app
    ```

4.  **Verify the Container is Running**: List the running containers.
    ```shell
    docker ps
    ```
    You should see your `my-flask-app` container running.

### 2.5 Configuring the AWS Security Group (Firewall)

Your container is running, but it's not yet accessible from the internet because the AWS security group (firewall) is blocking incoming traffic on port 80. You need to add a rule to allow HTTP traffic.

1.  **Navigate to EC2 Security Groups**: In the AWS Management Console, go to the EC2 dashboard and select "Security Groups" from the left-hand menu under "Network & Security".

2.  **Select Your Security Group**: Find and select the security group associated with your instance. If you used the Console method, it might be named something like `launch-wizard-1`. If you used the CLI method, it will be `cli-sg`. You can identify the correct security group by looking at the "Security groups" field in your instance's details.

3.  **Edit Inbound Rules**: Click the "Inbound rules" tab and then click the "Edit inbound rules" button.

4.  **Add HTTP Rule**:
    *   Click "Add rule".
    *   For "Type", select **HTTP** from the dropdown menu. This will automatically set the protocol to TCP and the port to 80.
    *   For "Source", select **Anywhere-IPv4** from the dropdown menu. This corresponds to the CIDR block `0.0.0.0/0`, which allows traffic from any IP address on the internet. **Note**: For production applications, you would typically restrict this to specific IP ranges or use a load balancer.

5.  **Save Rules**: Click the "Save rules" button.

### 2.6 Accessing Your Application

1.  **Get Public IP**: In the EC2 dashboard, select your instance and find the "Public IPv4 address" in the details pane.

2.  **Open in Browser**: Open a new browser tab and navigate to `http://your-public-ip` (replace `your-public-ip` with the actual IP address).

You should see the message: **"Hello from your Docker container!"** along with the container's hostname. Congratulations! You have successfully deployed a containerized web application on AWS.

### 2.7 Connecting to the Running Container

You can also connect to the running container to inspect its environment or run commands inside it.

1.  **Get the Container ID**: Run `docker ps` to see the list of running containers. Copy the Container ID (the first column).

2.  **Execute a Shell Inside the Container**: Use the `docker exec` command to start a bash shell inside the container, replacing `<container-id>` with your actual container ID:
    ```shell
    docker exec -it <container-id> /bin/bash
    ```
    You are now inside the container. You can explore the file system, check environment variables, etc. Type `exit` to leave the container shell.

---

## Part 3: MCP Server Setup & AI Agent Foundation

This final part of the lab transitions from standard infrastructure setup to building the foundation for an AI agent. We will deploy a **Model Context Protocol (MCP) server** on AWS using serverless technologies (AWS Lambda and API Gateway). This server will act as a secure bridge, allowing a Large Language Model (LLM) like Claude to interact with your AWS environment and perform tasks on your behalf.

### 3.1 What is the Model Context Protocol (MCP)?

The Model Context Protocol (MCP) is an open standard that enables LLMs to securely and reliably connect to external tools, APIs, and data sources [4]. Instead of building custom, one-off integrations for every tool, MCP provides a standardized way for an AI agent to:

*   **Discover** the tools available on a server.
*   **Understand** what the tools can do (their inputs, outputs, and descriptions).
*   **Execute** the tools with the correct parameters.
*   **Receive** the results and incorporate them into the agent's reasoning.

By deploying an MCP server, you are essentially giving your AI agent a secure gateway to perform actions within your AWS account, controlled by you. The agent can request to use a tool, and the MCP server will execute it on the agent's behalf, returning the results.

### 3.2 Deploying a Serverless MCP Server on AWS

We will use a sample serverless implementation from AWS that runs on **AWS Lambda** and **Amazon API Gateway**. This architecture is highly cost-effective and falls within the **Always Free** tier for a significant amount of usage (1 million Lambda requests and 1 million API Gateway requests per month for the first 12 months, and 1 million Lambda requests per month always free) [1] [11].

**Prerequisites**:

*   Ensure you have the **AWS CLI** configured from Part 1.
*   You also need to install the **AWS Serverless Application Model (SAM) CLI** on your local machine.

#### 3.2.1 Installing the AWS SAM CLI

1.  **Install the AWS SAM CLI**: Follow the official guide for your operating system: [Installing the AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html). The SAM CLI is a command-line tool for building and deploying serverless applications on AWS.

2.  **Verify Installation**: After installation, verify that SAM is installed correctly by running:
    ```shell
    sam --version
    ```
    You should see output like `SAM CLI, version 1.x.x`.

#### 3.2.2 Cloning and Deploying the Sample MCP Server

1.  **Clone the Sample Repository**: On your **local machine** (not the EC2 instance), clone the sample project from GitHub.
    ```shell
    git clone https://github.com/aws-samples/sample-serverless-mcp-servers.git
    cd sample-serverless-mcp-servers/stateless-mcp-on-lambda-python
    ```

2.  **Build the Application**: The SAM CLI will package the Lambda function and its dependencies.
    ```shell
    sam build
    ```
    This command will create a `.aws-sam` directory with the built artifacts.

3.  **Deploy the Application**: This command will guide you through deploying the serverless stack to AWS.
    ```shell
    sam deploy --guided
    ```
    You will be prompted with several questions:
    *   **Stack Name**: Enter `my-mcp-server` (or any name you prefer).
    *   **AWS Region**: Enter your preferred region (e.g., `us-east-1`). Use the same region where you created your EC2 instance.
    *   **Confirm changes before deploy**: Enter `y` (yes).
    *   **Allow SAM CLI IAM role creation**: Enter `y` (yes). SAM needs to create IAM roles for the Lambda function.
    *   **Disable rollback**: Enter `n` (no). This allows you to debug if the deployment fails.
    *   **McpLambdaFunction has no authentication. Is this okay?**: Enter `y` (yes). For this lab, we are not adding authentication to the MCP server. In production, you would add authentication.
    *   **Save arguments to configuration file**: Enter `y` (yes).
    *   **SAM configuration file**: Press Enter to accept the default `samconfig.toml`.
    *   **SAM configuration environment**: Press Enter to accept the default `default`.

    SAM will now create the necessary IAM roles, the API Gateway endpoint, and the Lambda function. After a few minutes, the deployment will complete.

4.  **Get Your MCP Server URL**: In the output of the `sam deploy` command, look for a line that says **"Outputs"** and then find the key **`McpApiEndpoint`**. The value will be a URL that looks something like this:
    ```
    https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/Prod/mcp
    ```
    **This is the URL of your MCP server. Copy it and keep it handy.** This is the endpoint that your AI agent will connect to.

### 3.3 Adding a Custom AWS Tool to Your MCP Server

Now we will add a simple tool to our MCP server that allows the AI agent to list your EC2 instances. This requires modifying the Lambda function code.

1.  **Navigate to the Lambda Function Code**: On your local machine, open the file located at:
    ```
    sample-serverless-mcp-servers/stateless-mcp-on-lambda-python/mcp_tools/app.py
    ```
    You can use any text editor (e.g., VS Code, Sublime Text, or even `nano` if you prefer).

2.  **Add Boto3 Import and a New Tool Function**: Boto3 is the AWS SDK for Python. We will add a function that uses it to describe EC2 instances. Add the following code to the `app.py` file. You can add it after the existing `@tool` definitions (if any) or at the end of the file, before the `if __name__ == "__main__":` block.

    ```python
    import boto3

    # ... (existing code) ...

    @tool
    def list_ec2_instances(region: str = "us-east-1") -> str:
        """Lists all EC2 instances in a specified AWS region.

        Args:
            region: The AWS region to check for instances (e.g., 'us-east-1', 'us-west-2').

        Returns:
            A string containing the instance IDs, types, and states, or an error message.
        """
        try:
            ec2 = boto3.client("ec2", region_name=region)
            response = ec2.describe_instances()
            instance_list = []
            for reservation in response["Reservations"]:
                for instance in reservation["Instances"]:
                    instance_id = instance["InstanceId"]
                    instance_type = instance["InstanceType"]
                    instance_state = instance["State"]["Name"]
                    instance_name = "N/A"
                    if "Tags" in instance:
                        for tag in instance["Tags"]:
                            if tag["Key"] == "Name":
                                instance_name = tag["Value"]
                    instance_list.append(
                        f"Name: {instance_name}, ID: {instance_id}, Type: {instance_type}, State: {instance_state}"
                    )
            if not instance_list:
                return f"No instances found in region {region}."
            return "\n".join(instance_list)
        except Exception as e:
            return f"Error listing EC2 instances: {str(e)}"
    ```

    Save the file.

3.  **Grant IAM Permissions**: The Lambda function needs permission to perform the `ec2:DescribeInstances` action. Open the `template.yaml` file in the root of the `stateless-mcp-on-lambda-python` directory.

    Find the section that defines the `McpLambdaFunction` resource. Within that resource, find the `Policies` section. Add the following policy statement to the list:

    ```yaml
          - Statement:
              - Effect: Allow
                Action:
                  - ec2:DescribeInstances
                Resource: "*"
    ```

    The `Policies` section should now look something like this (it may have other policies as well):

    ```yaml
      McpLambdaFunction:
        Type: AWS::Serverless::Function
        Properties:
          # ... other properties ...
          Policies:
            - Statement:
                - Effect: Allow
                  Action:
                    - ec2:DescribeInstances
                  Resource: "*"
    ```

    Save the file.

4.  **Redeploy the Application**: Now that you have added the new tool and permissions, rebuild and redeploy the server with the changes.
    ```shell
    sam build && sam deploy
    ```
    This time, SAM will use the saved configuration from `samconfig.toml`, so you won't be prompted with questions again. It will update the existing stack.

### 3.4 Connecting Your AI Agent (Claude Code)

Now you can connect an MCP-compatible client, like **Claude Code**, to your new tool server.

1.  **Open Claude Code**: If you don't have Claude Code installed, you can download it from the official Anthropic website or use the Claude.ai web interface (if MCP support is available there).

2.  **Add an MCP Server**: In Claude Code, go to the settings or preferences. Look for a section related to "Tools", "MCP Servers", or "Integrations".

3.  **Enter Your MCP Server URL**: You will need to add your MCP server. The exact steps may vary depending on the version of Claude Code, but generally, you will:
    *   Click "Add Server" or a similar button.
    *   Enter a name for your server (e.g., "My AWS MCP Server").
    *   Paste the `McpApiEndpoint` URL you saved earlier (e.g., `https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/Prod/mcp`).
    *   Save the configuration.

Your AI agent will now connect to your MCP server and discover the available tools.

### 3.5 Testing Your AWS AI Agent

Now, you can give your agent a prompt to test its new capability. In your Claude Code chat interface, type a prompt like this:

> "Using your tools, please list my EC2 instances in the us-east-1 region."

**What should happen:**

1.  **Tool Discovery**: Claude Code will connect to your MCP server and discover the `list_ec2_instances` tool.
2.  **Intent Recognition**: The agent will understand that it can fulfill your request using this tool.
3.  **Tool Execution**: The agent will call the `list_ec2_instances` tool via your MCP server, passing the `region` parameter as `us-east-1`.
4.  **Lambda Invocation**: The MCP server (API Gateway) will invoke your Lambda function.
5.  **AWS SDK Call**: The Lambda function will use the Boto3 SDK to call the `ec2:DescribeInstances` API.
6.  **Result Retrieval**: The Lambda function will receive the list of instances from AWS and format it as a string.
7.  **Response to Agent**: The result will be returned to the MCP server, which will send it back to Claude Code.
8.  **Presentation to User**: Claude Code will present the list of your EC2 instances to you in the chat.

You should see a response from Claude that includes the details of your running EC2 instances (the ones you created in Part 1).

**Congratulations!** You have successfully built and tested a foundational AI agent for AWS. This agent can now interact with your AWS account programmatically, and you can extend it by adding more tools to the MCP server.

### 3.6 Extending Your AI Agent (Next Steps)

Now that you have a working MCP server and AI agent, you can extend its capabilities by adding more tools. Here are some ideas:

*   **Start/Stop EC2 Instances**: Add tools to start, stop, or reboot instances.
*   **S3 Bucket Management**: Add tools to list, create, or delete S3 buckets, or to upload and download files.
*   **CloudWatch Metrics**: Add tools to retrieve metrics and logs from CloudWatch.
*   **Lambda Function Management**: Add tools to list, invoke, or update Lambda functions.
*   **Cost Monitoring**: Add tools to retrieve billing information and cost estimates.

Each new tool requires:

1.  Adding a new `@tool` decorated function in `app.py`.
2.  Granting the necessary IAM permissions in `template.yaml`.
3.  Redeploying the application with `sam build && sam deploy`.

By following this pattern, you can create a powerful AI agent that can manage and automate a wide range of AWS tasks, all within the Free Tier limits.

---

## Conclusion

In this lab, you have learned how to:

*   Set up an AWS account and understand the Free Tier limits (12-month free vs. always free).
*   Launch EC2 instances using both the AWS Management Console and the AWS CLI.
*   Connect to EC2 instances via SSH.
*   Install and configure Docker on an EC2 instance.
*   Create, containerize, and deploy a web application.
*   Configure AWS Security Groups to control network access.
*   Deploy a serverless Model Context Protocol (MCP) server using AWS Lambda and API Gateway.
*   Add custom AWS tools to your MCP server.
*   Connect an AI agent (Claude Code) to your MCP server and test its capabilities.

You now have a solid foundation for building AI agents that can interact with AWS services. This is a powerful starting point for automation, infrastructure management, and building intelligent cloud applications.

---

## References

[1] [AWS Free Tier](https://aws.amazon.com/free/)  
[2] [Track your Free Tier usage for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-free-tier-usage.html)  
[3] [Amazon EC2 T2 Instances](https://aws.amazon.com/ec2/instance-types/t2/)  
[4] [Unlocking the power of Model Context Protocol (MCP) on AWS](https://aws.amazon.com/blogs/machine-learning/unlocking-the-power-of-model-context-protocol-mcp-on-aws/)  
[11] [AWS Free Tier - Free Compute Services](https://aws.amazon.com/free/compute/)  
[12] [AWS Free Tier now includes 750 hours of free Public IPv4 addresses](https://aws.amazon.com/about-aws/whats-new/2024/02/aws-free-tier-750-hours-free-public-ipv4-addresses/)
