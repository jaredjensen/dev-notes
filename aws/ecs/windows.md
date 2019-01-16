# Windows ECS

## Containerize the .NET Application

- Works for both ASP.NET and EXE apps
- Can use Visual Studio's "Add Docker Support" feature or manually create Dockerfile

## Basic Steps

1. Build Docker image
2. Push image to ECR
3. Create cluster (**EC2 Windows + Networking** option)
   - Name
   - On-Demand vs Spot instances
   - Instance type
   - Number of instances
   - Storage type/size
   - SSH key pair
   - VPC
   - Security group
   - IAM role
4. (Auto-scaling group is created)
5. Create Task Definition
   - Container name
   - Image
   - Memory
   - Port mappings (for public access like websites)
   - CPU units
   - Entry point
   - Environment variables
   - Volumes
6. Create Service
   - Task Definition
   - Number of Tasks
   - Minimum healthy percentage

## CI/CD

Build agent (EC2 instance) should be given the minimum permission required.

1. Changes are committed to VCS
2. Build agent gets code from VCS and builds Docker image
3. Build agent publishes image to ECR
4. ECS deploys new containers with updated image
5. Once container health checks pass, load balancer directs traffic to them

## TFS

- Create a Personal Access Token (PAT) for build agent
- Once registered, build agent will appear in Agent Pools
- Go to **Build & Release** to set up build steps
  - Get source
  - NuGet restore
  - Build solution
  - Publish to ECR
