# opg-ecs-helper
ECS Helper in Golang: Managed by opg-org-infra &amp; Terraform

## Function 

### Stabilizer 

Check to make sure that the tasks for the list of ECS Services provided have 
stabilized after a deployment.

### Runner

Run ecs run-tasks from the commandline 

## Building Locally

```go
go build -mod vendor ./cmd/runner
```

```go
go build -mod vendor ./cmd/stabilizer
```
## Usage

### Stabilizer

You need to provide output containing a list of ECS services you want the stabilizer
to check.

```terraform
output "Role" {
  value = "arn:aws:iam::${local.account.id}:role/${var.default_role}"
}

output "Services" {
  value = {
    Cluster = aws_ecs_cluster.my_cluster.name
    Services = [
      aws_ecs_service.my_cool_service.name
    ]
  }
}
```

By default it will look for the outputs in `terraform.output.json`. 
Running `terraform output -json > terraform.output.json` will create the file for you.
```bash
aws-vault exec identity -- ecs-stabilizer
```

### Runner

You need to provide a output of the tasks you want to be able to 
run using the runner tool:

```terraform
output "Role" {
  value = "arn:aws:iam::${local.account.id}:role/${var.default_role}"
}

output "Task" {
  value = {
    Cluster    = var.cluster_name
    LaunchType = "FARGATE"
    NetworkConfiguration = {
      AwsvpcConfiguration = {
        SecurityGroups = [var.security_group_id]
        Subnets        = var.subnet_ids
      }
    }
    TaskDefinition = aws_ecs_task_definition.task.arn
  }
}

```

By default it will look for the outputs in `terraform.output.json`. 
Running `terraform output -json > terraform.output.json` will create the file for you.

```bash
aws-vault exec identity -- ecs-runner -task <task_name>
```
