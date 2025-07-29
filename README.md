# Deploying Grafana on Amazon ECS using Fargate
This project demonstrates how to deploy the open-source Grafana dashboard tool using Amazon ECS (Elastic Container Service) with the Fargate launch type. Grafana is containerized using the official Docker image and exposed to the internet through port 3000.

## Deployment Overview
- **Cloud Provider**: AWS
- **Container Orchestration**: Amazon ECS (Fargate)
- **Image**: [grafana/grafana](https://hub.docker.com/r/grafana/grafana)
- **Access Port**: 3000 (default Grafana dashboard)
- **Security Group**: Opened port 3000 to public IPs (for testing)
- **Public Access**: Enabled via public subnet and ENI public IP

## Steps to Deploy

1. **Create a Security Group**
   - Allow inbound TCP traffic on port 3000 from `0.0.0.0/0`
   - Name: `grafana-sg`
     
     ![Security Group](Screenshots/Security%20group.JPG)

2. **Create a Task Definition**
   - Task name: `grafana-task`
   - Image: `grafana/grafana:latest`
   - Port: 3000
   - CPU: 0.25 vCPU, Memory: 0.5 GB
     
     ![Task Definition](Screenshots/Task%20definition.JPG)

3. **Create an ECS Cluster**
   - Name: `grafana-cluster`
   - Type: Networking only (Fargate)

4. **Deploy Service**
   - Use `grafana-task`
   - Assign to a **public subnet**
   - **Enable auto-assign public IP**
   - Attach `grafana-sg`
     
     ![ECS Cluster and Service](Screenshots/ECS%20Cluster%20and%20running%20service.JPG)

5. **Access Grafana**
   - Go to the ENI (Elastic Network Interface) of the running task
   - Copy the public IP
   - Visit `http://<PUBLIC-IP>:3000` in your browser
   - Default login: `admin` / `admin`
     
     ![Grafana Login](Screenshots/Grafana%20login%20page.JPG)

## Challenges Faced
1. **Public IP not assigned initially**  
  The first deployment failed to expose Grafana to the internet because the service was launched without enabling the "Auto-assign Public IP" setting.

2. **Confusion with Private vs Public IP**  
  The ENI initially displayed only a private IP (e.g., 172.x.x.x), which led to failed browser access until the task was redeployed with a public IP.

3. **Port not open in Security Group**  
  Access was blocked when the security group did not include an inbound rule for port 3000. Updating the rule fixed the issue.

4. **Understanding ECS Networking**  
  Learning the relationship between VPCs, subnets, and ENIs was essential to properly expose the service to the public.

## Outcome
Grafana was successfully deployed and made publicly accessible via ECS Fargate. The setup demonstrated a foundational understanding of:
- AWS VPC networking
- Container orchestration with ECS
- Cloud security with security groups
- Docker image deployment on serverless infrastructure
