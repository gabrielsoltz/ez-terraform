# ez-terraform

**ez-terraform** is a simple wrapper around terraform to make it easier to manage multiple services, environments, and regions.

For each service, you can define multiple environments and regions. Each environment and region can have its own `terraform.tfvars`, and `secrets.tfvars` files while the terraform code is shared between all environments and regions. This way, you define the code for your service once, and then you can deploy it to multiple environments and regions.

To start, you need to create a new service. This will initiate the directory structure for your service and will create the first environment and region for you. Then, you can add more environments and regions to your service.

You must specify the s3 bucket name where the terraform state files will be stored. This bucket needs to be created manually before you can use this tool. You can use the same bucket for different environments and regions, **ez-terraform** will create a different key for each environment and region.

The remote configuration will be:

```hcl
bucket = "YOUR_BUCKET_NAME"
key    = "SERVICE/ENV/REGION/terraform.tfstate"
region = "REGION"
```

## Create a new service

```bash
$ ./ez-terraform create-service
Enter Service Name (e.g. my-new-service): service-backend
Enter Environment Name (e.g. stg or prd): dev
Enter Region Name (e.g. us-east-1 or eu-west-1): us-east-1
Enter Backend S3 Bucket Name (e.g. my-terraform-bucket): my-terraform-state-bucket

Creating service: service-backend
Creating environment: dev - OK
Creating region: us-east-1 - OK
Service service-backend created successfully:

    service-backend
    ├── outputs.tf
    ├── terraform.tf
    ├── variables.tf
    └── vars
        ├── dev
        │   ├── secrets.tfvars
        │   ├── terraform.tfvars
        │   └── us-east-1
        │       ├── remote_backend.tfvars
        │       ├── secrets.tfvars
        │       └── terraform.tfvars
        ├── secrets.tfvars
        └── terraform.tfvars

4 directories, 10 files
```

## Directory Structure

```bash
├── ez-terraform
├── service-backend
│   ├── < YOUR TERRAFORM CODE LIVES HERE >
│   ├── outputs.tf
│   ├── terraform.tf
│   ├── variables.tf
│   └── vars
│       ├── secrets.tfvars
│       ├── terraform.tfvars
│       ├── stg
│       │   ├── us-east-1
│       │   │   ├── remote_backend.tfvars
│       │   │   ├── secrets.tfvars
│       │   │   └── terraform.tfvars
│       │   ├── secrets.tfvars
│       │   └── terraform.tfvars
│       ├── prd
│       │   ├── us-east-1
│       │   │   ├── remote_backend.tfvars
│       │   │   ├── secrets.tfvars
│       │   │   └── terraform.tfvars
│       │   ├── secrets.tfvars
│       │   └── terraform.tfvars
│       └── dev
│           ├── us-east-1
│           │   ├── remote_backend.tfvars
│           │   ├── secrets.tfvars
│           │   └── terraform.tfvars
│           ├── secrets.tfvars
│           └── terraform.tfvars
└── another-service
```

## Usage

```bash
./ez-terraform <service> <environment> <region> <action> [OPTIONS]
```

## Examples

### Terraform Plan

```bash
./ez-terraform service-backend stg us-east-1 plan
```

### Terraform Apply

```bash
./ez-terraform service-backend prd us-east-1 apply
```

### Terraform Init

```bash
./ez-terraform service-backend dev us-east-1 init
```
