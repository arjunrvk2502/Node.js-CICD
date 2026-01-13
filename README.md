Node.js CI/CD with GitHub Actions and GCP Compute Engine
A demonstration project showing how to deploy a Node.js application on GCP Compute Engine with automated CI/CD using GitHub Actions and Docker.

📋 Overview
This project demonstrates a complete CI/CD pipeline that:

Containerizes a Node.js application using Docker
Automatically builds and pushes Docker images to Docker Hub
Deploys the application to a GCP Compute Engine instance
Triggers on every push to the main branch
🚀 Features
Automated Deployment: Push code changes and see them live automatically
Docker Containerization: Application runs in isolated Docker containers
GitHub Actions: CI/CD pipeline managed through GitHub workflows
GCP Compute Engine Hosting: Application hosted on a scalable VM instance
📁 Project Structure
.
├── .github/
│   └── workflows/
│       └── ci-cd.yml          # GitHub Actions workflow
├── Dockerfile                  # Docker configuration
├── package.json               # Node.js dependencies
├── index.js                   # Main application file
└── README.md                  # Project documentation
🛠️ Prerequisites
Google Cloud Platform (GCP) account
Docker Hub account
GitHub account
Basic knowledge of Node.js, Docker, and GCP
⚙️ Setup Instructions
1. GCP Compute Engine Setup
Create a VM Instance:

Go to Compute Engine → VM instances
Click Create Instance
Name: cicd-demo
Machine type: e2-micro (free tier eligible)
Boot disk: Ubuntu 22.04 LTS
Allow HTTP and HTTPS traffic
Click Create
Configure Firewall Rules:

Go to VPC Network → Firewall
Create a rule to allow port 3000:
Name: allow-app-port
Targets: All instances in the network
Source IP ranges: 0.0.0.0/0
Protocols and ports: tcp:3000
Connect to the instance via SSH (from GCP Console or local terminal)

Install Docker:

bash

Copy
curl -fsSL https://get.docker.com | bash
Add user to Docker group:

bash

Copy
sudo usermod -aG docker $USER
Exit and reconnect for changes to take effect

2. SSH Key Setup for GitHub Actions
Generate SSH key pair (on your local machine):

bash

Copy
ssh-keygen -t rsa -b 4096 -f ~/.ssh/gcp-cicd-key
Add public key to GCP:

Copy the content of ~/.ssh/gcp-cicd-key.pub
Go to Compute Engine → Metadata → SSH Keys
Click Edit → Add Item
Paste the public key
Save
Save private key for GitHub Secrets (you'll need the content of ~/.ssh/gcp-cicd-key)

3. GitHub Repository Setup
Create a new GitHub repository
Push your code to the repository
Navigate to Settings → Secrets and variables → Actions
Add the following repository secrets:
DOCKER_USERNAME: Your Docker Hub username
DOCKER_PASSWORD: Your Docker Hub password
GCP_HOST: External IP of your Compute Engine instance
GCP_USER: Your GCP username (usually matches your email prefix)
GCP_SSH_KEY: Contents of your private key file (gcp-cicd-key)
4. Update GitHub Actions Workflow
Update your .github/workflows/ci-cd.yml to use GCP secrets:

yaml

Copy
- name: Deploy to GCP
  uses: appleboy/ssh-action@master
  with:
    host: ${{ secrets.GCP_HOST }}
    username: ${{ secrets.GCP_USER }}
    key: ${{ secrets.GCP_SSH_KEY }}
    script: |
      docker pull ${{ secrets.DOCKER_USERNAME }}/node-app:latest
      docker stop my-app || true
      docker rm my-app || true
      docker run -d --name my-app -p 3000:3000 ${{ secrets.DOCKER_USERNAME }}/node-app:latest
5. Local Development
Clone the repository
Install dependencies:
bash

Copy
npm install
Test locally:
bash

Copy
node index.js
🐳 Docker Commands
Build the Docker image:

bash

Copy
docker build -t cicd-test .
Run the container:

bash

Copy
docker run -d --name demo -p 3000:3000 cicd-test
View running containers:

bash

Copy
docker ps
Check logs:

bash

Copy
docker logs <container-id>
🔄 CI/CD Pipeline
The GitHub Actions workflow automatically:

Checks out code from the repository
Sets up Node.js environment (v18)
Installs dependencies with npm
Builds Docker image with your Docker Hub username
Logs into Docker Hub using secrets
Pushes image to Docker Hub
Connects to GCP VM via SSH
Pulls latest image from Docker Hub
Stops and removes old container (if exists)
Runs new container with updated code
🌐 Accessing the Application
Once deployed, access your application at:

http://<GCP_EXTERNAL_IP>:3000
You can find your external IP in the GCP Console under Compute Engine → VM instances.

🔒 Security Notes
⚠️ Important: This setup is for learning purposes only!

For production environments:

Use more restrictive firewall rules (specific source IPs)
Implement proper authentication and authorization
Use HTTPS with SSL certificates
Consider using Google Cloud Secret Manager for sensitive data
Use Cloud Load Balancer for better scalability
Enable Cloud Armor for DDoS protection
Use service accounts with minimal required permissions
💰 Cost Management
Free Tier Benefits:

GCP offers an e2-micro instance free in certain regions
Monitor usage in Billing section
Set up budget alerts to avoid unexpected charges
🧹 Cleanup
To avoid GCP charges:

Stop or delete the VM instance:

Go to Compute Engine → VM instances
Select your instance
Click Delete
Delete firewall rules (if no longer needed)

Remove SSH keys from metadata

📝 Troubleshooting
Permission denied errors with Docker:

bash

Copy
sudo usermod -aG docker $USER
# Exit and reconnect
Port already in use:

bash

Copy
docker stop <container-name>
docker rm <container-name>
Cannot connect via SSH from GitHub Actions:

Verify SSH key is correctly added to GCP metadata
Check firewall allows SSH (port 22)
Ensure external IP is correct in secrets
Verify username matches GCP user
GitHub Actions failing:

Verify all secrets are correctly set
Check VM instance is running
Ensure firewall rules allow necessary ports
Check Docker is installed and running on VM
Connection timeout:

Verify firewall rules allow port 3000
Check VM instance external IP hasn't changed
Ensure application is running inside container
🔍 Useful GCP Commands
Connect to VM via gcloud CLI:

bash

Copy
gcloud compute ssh <instance-name> --zone=<zone>
View VM logs:

bash

Copy
gcloud compute instances get-serial-port-output <instance-name>
List firewall rules:

bash

Copy
gcloud compute firewall-rules list
🤝 Contributing
Feel free to fork this project and submit pull requests for improvements!

📄 License
This project is open source and available for educational purposes.

