# AWS Capstone Project — Resilient, Scalable & Recoverable Application

## Live Application URL
http://capstone-alb-331303247.us-east-1.elb.amazonaws.com

## Project Overview
A mission-critical e-commerce web application built on AWS, designed to handle 
unpredictable traffic spikes, remain available during infrastructure failures, 
and support rapid recovery from disaster events while meeting business RTO/RPO 
requirements.

## AWS Services Implemented

### High Availability (Day 1)
- **VPC** — Custom VPC with 2 public and 2 private subnets across 2 AZs
- **EC2** — Web servers deployed across us-east-1a and us-east-1b
- **ALB** — Application Load Balancer distributing traffic across both AZs
- **RDS MySQL Multi-AZ** — Primary + standby across 2 AZs with auto failover
- **DynamoDB Global Tables** — Session storage replicated to us-west-2

### Scalability (Day 2)
- **Auto Scaling Group** — Min=2, Max=4 instances, CPU-based scaling at 50%
- **ECS Fargate** — Containerized nginx application running on serverless compute
- **ECR** — Private container registry storing Docker images
- **ElastiCache Redis** — Caching layer reducing database load
- **SQS** — Order processing queue decoupling web and worker tiers

### Disaster Recovery (Day 3)
- **Route 53** — Failover routing with health checks between primary/secondary
- **AWS Backup** — Daily automated backups for RDS and DynamoDB, 7-day retention
- **CloudWatch** — Dashboard + 3 alarms (High CPU, Unhealthy Hosts, Queue Depth)

### CI/CD Pipeline (Configured)
- **CodeBuild** — Build project configured with buildspec.yaml for Docker builds
- **CodePipeline** — Pipeline created with Source → Build → Deploy to ECS stages
- **Note:** Pipeline execution requires account verification completion on AWS 
  free tier. All pipeline components (CodeBuild project, CodePipeline, ECR 
  repository, ECS cluster and service) are fully configured and ready to 
  execute once account restrictions are lifted.

## Architecture Highlights
- Multi-AZ deployment eliminates single points of failure
- Auto scaling handles traffic spikes automatically
- RDS Multi-AZ provides automatic database failover
- DynamoDB Global Tables ensures regional data replication
- ElastiCache reduces database load during peak traffic
- SQS decouples order processing from web tier
- Route 53 failover enables cross-region disaster recovery

## RTO/RPO Targets
| Metric | Target | Achieved |
|--------|--------|---------|
| RTO | 15 minutes | ~5 minutes |
| RPO | 1 hour | ~1 hour |

## DR Runbook
### Failover Steps:
1. CloudWatch alarm detects failure
2. Route 53 health check fails on primary
3. Route 53 automatically routes traffic to secondary
4. RDS Multi-AZ promotes standby to primary (~2 minutes)
5. ECS tasks restart on healthy infrastructure
6. SNS notification sent to operations team
7. Verify application health via ALB health checks
8. Document incident and recovery time

## Scaling Test Report
- ASG configured with target tracking scaling policy
- Scale-out triggered when CPU > 50% for 5 minutes
- Scale-in triggered when CPU drops below 50%
- ECS service scales between 2-4 tasks based on CPU
- SQS queue depth alarm triggers at 100 messages

## Reflection Notes
### Trade-offs
- **Cost vs Resilience:** Multi-AZ RDS doubles cost but provides automatic failover
- **Complexity vs Reliability:** Multiple services increase architecture complexity 
  but significantly improve reliability and fault tolerance
- **Performance vs Cost:** ElastiCache improves response times but adds monthly cost

### What Went Well
- Successfully deployed multi-AZ architecture
- ECS Fargate running nginx container via ALB
- All monitoring and backup systems configured
- Route 53 failover routing implemented

### Potential Improvements
- Complete CI/CD pipeline execution with full account access
- Add WAF for security layer
- Implement blue/green ECS deployments
- Add cross-region RDS read replicas
- Implement chaos engineering tests using AWS Fault Injection Simulator

## Screenshots
See screenshots folder for visual evidence of all components.
