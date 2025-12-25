# RDS Infrastructure Database

This Terraform configuration provisions AWS RDS instances using the [terraform-aws-modules/rds/aws](https://github.com/terraform-aws-modules/terraform-aws-rds) module.

## Environments

- **QA**: Single RDS instance with no read replicas
- **Production**: RDS instance with 1 read replica

## Prerequisites

- Terraform >= 1.0
- AWS CLI configured with appropriate credentials
- VPC and subnets already created in your AWS account

## Configuration

### Required Variables

Before applying, update the following variables in the `.tfvars` files:

1. **VPC Configuration**:
   - `vpc_id`: Your VPC ID
   - `vpc_cidr`: Your VPC CIDR block
   - `subnet_ids`: List of subnet IDs (at least 2 in different availability zones)

2. **Database Credentials**:
   - `username`: Master database username
   - `password`: Master database password (change from default!)

3. **AWS Region**:
   - `aws_region`: AWS region where resources will be created

## Usage

### Initialize Terraform

```bash
terraform init
```

### Plan for QA Environment

```bash
terraform plan -var-file="qa.tfvars" -out=qa.tfplan
```

### Apply for QA Environment

```bash
terraform apply qa.tfplan
```

### Plan for Production Environment

```bash
terraform plan -var-file="prod.tfvars" -out=prod.tfplan
```

### Apply for Production Environment

```bash
terraform apply prod.tfplan
```

## Environment Differences

| Configuration | QA | Production |
|--------------|----|-----------| 
| Instance Class | db.t3.micro | db.t3.small |
| Allocated Storage | 20 GB | 100 GB |
| Max Storage | 100 GB | 500 GB |
| Read Replicas | 0 | 1 |
| Backup Retention | 7 days | 30 days |

## Outputs

After applying, you can retrieve the following outputs:

- `db_instance_endpoint`: Connection endpoint
- `db_instance_address`: Database address
- `db_instance_port`: Database port
- `db_instance_name`: Database name
- `db_instance_arn`: RDS instance ARN
- `db_replica_endpoints`: Read replica endpoints (production only)

## Security Notes

1. **Passwords**: Always use strong, unique passwords. Consider using AWS Secrets Manager for production.
2. **Network**: Instances are configured as `publicly_accessible = false` by default. Adjust security groups as needed.
3. **Encryption**: Storage encryption is enabled by default.
4. **Backups**: Ensure backup retention periods meet your compliance requirements.

## Cleanup

To destroy the infrastructure:

```bash
# QA
terraform destroy -var-file="qa.tfvars"

# Production
terraform destroy -var-file="prod.tfvars"
```

## Module Documentation

For detailed module documentation, see: https://github.com/terraform-aws-modules/terraform-aws-rds

