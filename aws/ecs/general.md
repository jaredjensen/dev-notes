# AWS Container Service

## Services

- ECR - Elastic Container Registry
- ECS - Elastic Container Service

## Concepts

### Repository

Location for storing containers.

### Cluster

Group of EC2 instances that run containers. Can scale up and out.

### Task Definition

A set of Docker container configurations. For example, a website Task Definition may include configurations for web server and database containers.

### Task

A set of running Docker containers, as defined by the Task Definition.

### Service

The desired state of a Task. ECS will ensure this state is maintained.

Settings:

- Deployment strategy
  - Bin packing (fill one EC2 instance, then next)
  - Balance spread (fill all EC2 instances evenly)
