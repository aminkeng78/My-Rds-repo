#  aws-rdscluster-conilius-tf

Terraform module which creates AWS RDS Aurora resources[aws_aurora-module](git::https://github.com/Bkoji1150/aws-rdscluster-conilius-tf.git).


## Available Features

- Autoscaling of read-replicas
- Global cluster
- Enhanced monitoring
- Serverless cluster
- Import from S3
- Fine grained control of individual cluster instances
- Custom endpoints
- secrets manager for database credentials
####  maste database user's password is managed by aws secrets manager

## Usage
Postgres-Aurora  module
```hcl

module "aurora" {
   source = "git::https://github.com/aminkeng78/aws-rdscluster-conilius-tf?ref=v1.1.11
   

  component_name = var.component_name
  name           = local.name
  engine         = "aurora-postgresql"
  engine_version = "11.12"
  instances = {
    1 = {
      instance_class      = "db.r5.2xlarge"
      publicly_accessible = false
    }
    2 = {
      identifier     = format("%s-%s", var.component_name, "writer-instance")
      instance_class = "db.r5.large"
      promotion_tier = 15
    }
  }
  endpoints = {
  }
  vpc_id                 = local.vpc_id
  db_subnet_group_name   = local.db_subnets_names
  create_db_subnet_group = false
  allowed_cidr_blocks    = local.private_sunbet_cidrs
  subnets                = local.private_subnets_ids

  create_security_group = true
  security_group_egress_rules = {
    to_cidrs = {
      cidr_blocks = ["0.0.0.0/0"]
      description = "Egress to corporate printer closet"
    }
  }
  iam_database_authentication_enabled = true

  apply_immediately   = true
  skip_final_snapshot = true

  enabled_cloudwatch_logs_exports = ["postgresql"]
  database_name                   = "postgres_aurora"
  master_username                 = var.master_username
}


```
Mysql-Aurora

```hcl
module "aurora" {
 source = "git::https://github.com/aminkeng78/aws-rdscluster-conilius-tf?ref=v1.1.11"

component_name = var.component_name
  name           = local.name
  engine         = "aurora-mysql"
  engine_version = "5.7.mysql_aurora.2.10.1"
  instances = {
    1 = {
      instance_class      = "db.r5.large"
      publicly_accessible = true
    }
    2 = {
      identifier     = format("%s-%s", "conilius-${var.component_name}", "reader-instance")
      instance_class = "db.r5.xlarge"
      promotion_tier = 15
    }
  }

  vpc_id                 = local.vpc_id
  db_subnet_group_name   = local.db_subnets_names

  create_db_subnet_group = false
  create_security_group  = true

  iam_database_authentication_enabled = true
  apply_immediately   = true
  skip_final_snapshot = true
  security_group_egress_rules = {
    to_cidrs = {
      cidr_blocks = ["0.0.0.0/0"]
      description = "Egress to corporate printer closet"
    }
  }

  enabled_cloudwatch_logs_exports = ["audit", "error", "general", "slowquery"]
  database_name                   = "postgres_aurora"
  master_username                 = var.master_username
}
  
```
```bash
$ terraform init
$ terraform plan
$ terraform apply
```
<!-- preety ignore start -->
<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | n/a |
| <a name="provider_random"></a> [random](#provider\_random) | n/a |
