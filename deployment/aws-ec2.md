---

# 🖥️ Deploying Spring Boot Applications to AWS EC2

Amazon EC2 (Elastic Compute Cloud) provides scalable virtual servers in the cloud, allowing you to run applications with full control over the underlying infrastructure. Deploying a Spring Boot application to an EC2 instance gives you the flexibility to customize your environment and scale your application as needed.

## 🎯 Why Use EC2?

- **Full Control**: Complete control over the operating system, software, and configurations.
- **Scalability**: Easily scale your application by adjusting the instance size or adding more instances.
- **Integration**: Seamlessly integrate with other AWS services like RDS, S3, and CloudWatch.

## 🛠️ Prerequisites

Before deploying to EC2, ensure you have the following:

- An AWS account ([Sign up here](https://aws.amazon.com/)).
- The AWS CLI installed on your Mac Mini (M1 chip).
- A key pair for SSH access to your EC2 instance.

### 1. **Installing AWS CLI**

If you haven't already installed the AWS CLI, follow these steps:

```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

Verify the installation:

```bash
aws --version
```

### 2. **Configure AWS CLI**

Configure your AWS CLI with your AWS account credentials:

```bash
aws configure
```

You will be prompted to enter your AWS Access Key ID, Secret Access Key, region, and output format.

## 🚀 Deploying Your Spring Boot Application on EC2

### 1. **Launch an EC2 Instance**

1. Go to the [EC2 Dashboard](https://console.aws.amazon.com/ec2/).
2. Click on "Launch Instance."
3. Select an Amazon Machine Image (AMI). For a Spring Boot application, choose an Amazon Linux 2 AMI (64-bit x86).
4. Choose an instance type. For most applications, a `t2.micro` instance (which is free-tier eligible) is sufficient.
5. Configure the instance details as needed, then click "Next: Add Storage."
6. Add storage as required and click "Next: Add Tags."
7. Add tags (optional) and click "Next: Configure Security Group."
8. Configure a security group to allow inbound traffic on port 8080 (or your application port) and SSH (port 22).
9. Review and launch the instance. You will be prompted to select or create a key pair for SSH access. Save the `.pem` file securely.

### 2. **Connect to Your EC2 Instance**

Use SSH to connect to your EC2 instance from your Mac Mini:

```bash
ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-dns
```

### 3. **Install Java on EC2**

Once connected, install the Java Development Kit (JDK):

```bash
sudo yum update -y
sudo amazon-linux-extras install java-openjdk17 -y
```

Verify the installation:

```bash
java -version
```

### 4. **Transfer Your Spring Boot Application**

Transfer your Spring Boot JAR file to the EC2 instance using `scp`:

```bash
scp -i /path/to/your-key.pem target/myapp.jar ec2-user@your-ec2-public-dns:/home/ec2-user/
```

### 5. **Run Your Spring Boot Application**

SSH into the EC2 instance and run your Spring Boot application:

```bash
java -jar /home/ec2-user/myapp.jar
```

Your application should now be running on port 8080 of your EC2 instance. You can access it via `http://your-ec2-public-dns:8080`.

### 6. **Configure EC2 Instance for Production**

For production deployments, consider the following configurations:

- **Set Up a Reverse Proxy**: Use Nginx or Apache as a reverse proxy to manage incoming traffic and provide SSL termination.
- **Run as a Service**: Configure your Spring Boot application to run as a service so it starts automatically on boot.
- **Monitoring and Logging**: Set up CloudWatch for monitoring and logging to track application performance and diagnose issues.
- **Automatic Backups**: Use AWS Backup or snapshots to automate backups of your EC2 instance.

### 7. **Scaling Your Application**

To scale your application, you can:

- **Resize the Instance**: Change the instance type to a larger size for more CPU, memory, or storage.
- **Auto Scaling**: Configure an Auto Scaling group to automatically add or remove instances based on demand.
- **Load Balancing**: Use an Elastic Load Balancer (ELB) to distribute traffic across multiple instances.

## 🔑 Best Practices

- **Secure Your Instance**: Use security groups to restrict access to your instance and apply the principle of least privilege.
- **Regular Updates**: Regularly update the software on your EC2 instances to protect against vulnerabilities.
- **Backup and Restore**: Implement regular backups and practice restoring from them to ensure quick recovery in case of failure.
- **Monitor Performance**: Use AWS CloudWatch to monitor your instance's performance and set up alarms for critical metrics.
- **Optimize Costs**: Choose the right instance type and consider using reserved instances for long-running applications to save costs.

## 📚 Further Reading

- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/index.html)
- [Deploying Spring Boot Applications on EC2](https://aws.amazon.com/getting-started/hands-on/deploy-spring-boot-app-ec2/)
- [Setting Up a Java Development Environment on EC2](https://aws.amazon.com/premiumsupport/knowledge-center/setup-java-jdk-ec2-linux/)
- [Amazon CloudWatch Documentation](https://docs.aws.amazon.com/cloudwatch/index.html)

---
