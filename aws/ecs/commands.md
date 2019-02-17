# AWS ECS Common Commands

## Create a cluster

```bash


```

## Get cluster information

```bash
$ aws ecs describe-clusters --cluster linux-test

{
    "clusters": [
        {
            "status": "ACTIVE",
            "statistics": [],
            "tags": [],
            "clusterName": "linux-test",
            "registeredContainerInstancesCount": 2,
            "pendingTasksCount": 0,
            "runningTasksCount": 0,
            "activeServicesCount": 0,
            "clusterArn": "arn:aws:ecs:us-west-2:589969683286:cluster/linux-test"
        }
    ],
    "failures": []
}
```

## Delete a cluster

To-do: Review this; I had trouble determining instance ids given a cluster.

```bash
# List EC2 instances in cluster
aws ecs list-container-instances \
  --cluster <cluster id>

# Option 1: Deregister EC2 instances in cluster
aws ecs deregister-container-instance \
  --cluster <cluster id> \
  --container-instance <instance id> \
  --region us-west-2 --force

# Option 2: Delete EC2 instances in cluster
# Automatically deregisters them
aws ec2 delete-instance deregister-container-instance \
  --cluster <cluster id> \
  --container-instance <instance id> \
  --region us-west-2 --force

# Delete the cluster
aws ec2 delete-cluster \
  --cluster <cluster id>
```