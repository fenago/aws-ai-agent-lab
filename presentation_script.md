# AWS AI Agent Lab Guide: Presentation Script

**Presenter**: Dr. Ernesto Lee

---

### Slide 1: Title

**(Start with a welcoming and engaging tone)**

"Hello everyone, and welcome. My name is Dr. Ernesto Lee. Have you ever wondered what it would be like to have an AI assistant that could manage your cloud infrastructure for you? An agent that could spin up servers, deploy applications, and monitor your systems, all based on your natural language commands? Today, we are going to build the foundation for exactly that.

This is the **Building an AWS AI Agent Foundation** lab. Over the next hour, we will demystify the cloud, containerization, and AI integration. We are going to take you from zero to a fully functional, cloud-native AI agent.

And the best part? **We will do all of this using only the free services that AWS provides.** This isn’t just a theoretical exercise; this is a hands-on lab that will give you practical, in-demand skills."

---

### Slide 2: What You'll Build Today

"So, what exactly will you have accomplished by the end of this session? This isn’t just about learning concepts; it’s about building something tangible. You will walk away with a complete, working environment.

You will have a **fully configured AWS Free Tier account**, ready for you to explore the cloud. You will have **running EC2 virtual machines**, and you’ll know how to launch them using multiple methods, from the simple visual console to powerful command-line automation. You will have **Docker containers deployed and accessible** from the internet, a cornerstone of modern application development.

And most importantly, you will have a **serverless MCP server connecting an AI to your AWS account.** This is the magic that allows your AI agent to manage your cloud infrastructure. **All of this will be done within the free tier limits**, so you can learn and experiment without worrying about a surprise bill."

---

### Slide 3: Understanding Free Tier

"Now, how is all of this free? It’s because we are going to be strategic in how we use the AWS Free Tier. But what does ‘free’ really mean? It’s crucial to understand that there are two main types of free tiers.

First, there’s the **12-Month Free** tier. This starts the day you create your account and gives you a generous amount of popular services, like 750 hours of an EC2 server, which is enough to run one 24/7. This is what we’ll use for our virtual machine. But, as the name implies, it expires after one year.

Second, there’s the **Always Free** tier. These are services that have a smaller free allocation, but it never expires. This includes services like AWS Lambda, where you get one million requests per month, forever. We’ll use this for our serverless AI integration. The key takeaway is this: **you must monitor your usage to avoid unexpected charges.** AWS provides tools for this, and we’ll point them out."

---

### Slide 4: Sign-In Methods

"Before we dive in, let’s talk about how you get into your AWS account. You might have seen a sign-in screen like the one on the right and wondered what it all means. What’s the difference between a Root User and an IAM User?

The **Root User** is you, the account owner. You use the email and password you signed up with. This user has complete, unrestricted access to everything. It’s for initial setup, managing billing, and high-level security changes.

An **IAM User**, on the other hand, is an additional user you create within your account, perhaps for a team member or an application. They have their own credentials and, crucially, you can grant them limited permissions. This is the best practice for security in a team environment. But for today, since you are the owner of your own account, **you will always sign in as the Root User.**"

---

### Slide 5: Three Ways to Launch EC2

"So, how do we create our first virtual machine in the cloud? There isn’t just one way; there are multiple paths, each with its own advantages. We are going to explore three distinct methods.

First, we’ll use the **AWS Console**, which is the web-based graphical interface. It’s visual, intuitive, and perfect for beginners. Second, we’ll use **Browser SSH**, also known as EC2 Instance Connect. This is a fantastic feature that lets you open a terminal to your server directly in your browser, with no local tools needed. It’s the easiest way to get connected.

Finally, we’ll use the **AWS CLI**, or Command Line Interface. This is how you automate and script your infrastructure. By learning all three, you’ll have a complete understanding of how to manage EC2, from your first click to your first script."

---

### Slide 6: EC2 Console Launch

"Let’s start with the console. When you launch an instance, you’re faced with a few key decisions. What are the most important ones?

First is the **AMI**, or Amazon Machine Image. Think of this as the operating system template. We’ll use **Ubuntu 22.04 LTS**, which is a popular, stable Linux distribution that’s part of the Free Tier. Next is the **Instance Type**. We’ll choose the **t2.micro**, which gives us one virtual CPU and one gigabyte of RAM—plenty for our lab and, again, free.

Then, the **Key Pair**. This is your digital key to unlock your server. You’ll create it and download a `.pem` file. Guard this file carefully! Finally, the **Security Group**. This is your cloud firewall. The most important rule we’ll set here is to allow SSH access from **‘My IP’ only.** This is a critical security measure. **Never expose SSH to the entire internet in a production environment.**"

---

### Slide 7: Browser-Based Connection

"What if you don’t have a terminal program on your computer, or you’re on a machine where you can’t install software? Does that mean you can’t connect to your server? Absolutely not.

AWS has a fantastic feature called **EC2 Instance Connect**. It’s the simplest way to get a command line on your server. You just select your instance in the dashboard, click ‘Connect’, choose ‘EC2 Instance Connect’, and click ‘Connect’ again.

And just like that, **a new browser tab opens with a fully functional terminal session.** There are no keys to manage, no software to install. It removes all the friction of getting connected, so you can get straight to work."

---

### Slide 8: Docker Introduction

"Now that we have our server, let’s talk about how we’re going to run our applications. Have you ever had a developer say, ‘Well, it works on my machine’? This is the exact problem that Docker was created to solve.

Docker is a containerization technology. It allows you to **package your application along with all of its dependencies**—libraries, configuration files, everything—into a single, isolated unit called a container. This container can then **run anywhere** that Docker is installed: your laptop, a server in your office, or a virtual machine in the cloud.

Containers are much more **lightweight than traditional virtual machines**, and they have become the **industry standard for modern application deployment.** In this lab, we’re going to do two things: we’ll build our own custom container from scratch, and we’ll also pull a pre-built, complex application directly from Docker Hub."

---

### Slide 9: Docker Hub Highlight

"So, what is Docker Hub? Think of it as the App Store, but for server applications. It’s a public registry with millions of pre-built container images that you can download and run, often with a single command. How powerful is this?

Let’s take a look at OpenClaw, which is a sophisticated, open-source AI assistant. To run it on our server, all we need are two commands. First, `docker pull 1panel/openclaw` to download the image. Then, `docker run` to start it. We map port 3000 on our server to port 80 inside the container.

And that’s it. In a matter of seconds, **you have a complete AI assistant running on your server.** This is the power of containerization and public registries. It dramatically accelerates development and deployment."

---

### Slide 10: Security Groups

"We’ve talked about launching servers and running containers, but how do we control who can access them? In AWS, the answer is **Security Groups**. You can think of a security group as a firewall that surrounds your virtual machine.

By default, it blocks all incoming traffic. You have to explicitly open the ports that you need. For our lab, we’ll configure three rules. We’ll open **port 22 for SSH**, but only from our own IP address. This is for secure management.

Then, we’ll open **port 80 for HTTP**, which is standard web traffic, from anywhere on the internet. This will be for our custom Flask application. And finally, we’ll open **port 3000 for OpenClaw**, also from anywhere, so we can access its web interface. This granular control is fundamental to cloud security."

---

### Slide 11: What is MCP?

"Now we get to the most exciting part. How do we bridge the gap between a Large Language Model like Claude and our AWS infrastructure? The answer is the **Model Context Protocol, or MCP.**

What is it? MCP is the missing link. It’s an **open standard designed specifically for LLM-to-tool integration.** Instead of building a fragile, custom integration for every tool, MCP provides a standardized way for an AI agent to **discover** what tools are available, **understand** what they can do, and **execute** them securely.

Think of it like this: if your AWS account is a fortress, and your tools are the powerful machines inside, then **MCP is the secure, intelligent gatekeeper** that allows your trusted AI agent to come inside and operate those machines on your behalf. It’s an API gateway, but designed for AI."

---

### Slide 12: MCP Architecture

"So how do we build this AI gateway? Are we going to set up more complex servers? No. We’re going to do it the modern, cloud-native way: serverless. Our MCP server will be built on two core AWS services: **Lambda and API Gateway.**

**AWS Lambda** is a service that runs your code on-demand, without you having to manage any servers. We’ll write our tool logic—like ‘list EC2 instances’—as a Lambda function. **API Gateway** then takes that function and exposes it as a secure HTTPS endpoint on the internet. When our AI agent calls that endpoint, API Gateway triggers the Lambda function, which then interacts with other AWS services using secure **IAM Roles.**

And what about the cost? This is where the Always Free tier comes in. **You get one million Lambda requests per month, for free, forever.** This makes it an incredibly cost-effective way to build a powerful AI agent foundation."

---

### Slide 13: Building Your First Tool

"How hard is it to add a new capability to our AI agent? Is it a multi-day coding project? Not at all. Let’s look at the code for our first tool: listing EC2 instances.

This is it. It’s a simple Python function. The `@tool` decorator tells the MCP server that this is a tool available to the AI. The function takes a region as input, uses the AWS SDK (`boto3`) to connect to EC2 and describe the instances, and then returns a formatted string.

That’s all there is to it. To add this to our agent, we simply **add this code, update the IAM permissions** to allow our Lambda function to perform the `describe-instances` action, and **re-deploy.** In just a few minutes, our AI agent has a new skill: it can now see and list our virtual machines."

---

### Slide 14: Connecting Claude Code

"We have our tool server, but how do we connect our AI to it? With an MCP-compatible client like Claude Code, it’s incredibly simple.

First, you deploy your MCP server using the SAM CLI, which we’ll walk through. This gives you a unique API Gateway endpoint URL. You copy that URL.

Then, in your Claude Code settings, you add a new tool server and paste in the URL. That’s it. Claude Code will automatically connect to your server, discover the `list_ec2_instances` tool, and understand how to use it. You can then simply ask it in plain English: **‘List my EC2 instances in us-east-1.’** And just like that, **your AI agent is now cloud-native.**"

---

### Slide 15: Extending Your AI Agent

"Listing instances is just the beginning. What else can you build? Once you have this foundation, the possibilities are endless. You can add more tools to give your agent more capabilities.

Imagine adding tools to **start and stop EC2 instances** to save costs. Or tools to **manage S3 buckets and files**, allowing your agent to handle data storage. You could add tools to **query CloudWatch metrics and logs**, turning your agent into a monitoring assistant. You could even give it the power to **deploy new Lambda functions** or **monitor your billing.**

And the best part is the simple, repeatable pattern. Every new tool is just three steps: **Code the function, add the IAM permissions, and deploy.** You can incrementally build an incredibly powerful and personalized AWS assistant."

---

### Slide 16: Best Practices

"As you move from this lab to building your own projects, what are the key best practices you should follow? How do you operate like a seasoned cloud professional?

First, and most importantly, **never use your root user for daily work.** Once your account is set up, create an IAM user for yourself with more limited permissions. Second, **enable Multi-Factor Authentication (MFA)** on both your root and IAM users. This is the single most effective thing you can do to protect your account.

Third, always follow the principle of **least-privilege.** Only grant the minimum permissions necessary for a user or service to do its job. Fourth, **tag everything.** Tags are metadata labels that help you organize your resources and, crucially, track your costs. And finally, **set billing alerts.** Configure AWS to notify you when your spending approaches a certain threshold. This will help you avoid any surprise charges."

---

### Slide 17: Cost Management

"Speaking of costs, how do you ensure you stay within the Free Tier? The key is to monitor the right metrics.

For **EC2**, you get 750 hours per month. This is enough for one `t2.micro` instance to run continuously. You can track this in the **AWS Billing Dashboard**, which has a Free Tier usage report.

For **Lambda**, you get one million requests per month, which is a huge number. You can monitor your usage in **CloudWatch Metrics.** And for **Data Transfer**, you get 100 gigabytes of data transfer out to the internet per month. You can track this in **Cost Explorer.** Staying on top of these three metrics will ensure your cloud learning journey remains free."

---

### Slide 18: Troubleshooting

"What happens when things go wrong? Inevitably, you’ll run into issues. What are some of the common ones and how do you solve them?

If you **can’t connect via SSH**, the first two places to look are your **security group**—is port 22 open from your IP?—and your **key file permissions.** Remember that `chmod 400` command.

If you get a **‘permission denied’ error with Docker**, it’s almost always because you forgot to log out and log back in after adding your user to the `docker` group. If your **container is running but you can’t access it** from your browser, it’s a security group issue. Check that you’ve opened the correct port.

And if your **MCP server is giving you errors**, the answer is almost always in the **CloudWatch Logs** for your Lambda function. **CloudWatch Logs are your best friend for debugging** any serverless application."

---

### Slide 19: Resources

"This lab is just the beginning of your journey. Where can you go to learn more?

First, the complete lab guide, including all the code and commands, is available on **GitHub** at the link shown here. The official **AWS Documentation** is an incredibly comprehensive resource for every service. The **MCP Specification** website has the full details on the protocol we used for our AI agent.

**Docker Hub** is where you can explore millions of other container images to run. And of course, the **AWS Free Tier** page has the full list of all the services you can experiment with at no cost."

---

### Slide 20: Thank You

"We’ve covered a lot of ground today. We went from creating a brand new AWS account to having a functional, cloud-native AI agent that can manage our infrastructure. You’ve learned how to launch servers, deploy containers, and build a serverless bridge for AI.

I hope this has demystified the cloud and shown you how accessible and powerful these technologies are. The foundation you’ve built today is the starting point for incredible automation and innovation.

Thank you for your time and attention. I’d now be happy to answer any questions you may have."
