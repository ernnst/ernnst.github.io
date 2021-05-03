---
layout: post
title: "Storing Terraform State in Consul"
date: 2021-05-03
tags: terraform consul
comments: true
twitter: true
summary: "HashiCorp products integrate seamlessly with each other, so storing Terraform state in a Consul cluster is very easy."
---

I was impressed that Terraform performs better with Consul backend instead of S3/DynamoDB.

HashiCorp products integrate seamlessly with each other, so storing Terraform state in a
Consul cluster is very easy. Just  create a policy, token and define the Terraform backend.

Here are the steps.

### Create Consul Policy

Example `policy.json`:

```json
{
  "Name": "terraform-state",
  "Description": "Grants required permissions to store Terraform state",
  "Rules": "session_prefix \"\" { policy = \"write\"}, key_prefix \"terraform-state/\" { policy = \"write\"}",
  "Datacenters": [
    "dc1"
  ]
}
```

Note: Key prefix is target location in Consul.
Session prefix is required for locking Terraform state in Consul.

Create example policy:

```bash
curl -X PUT --data @policy.json \
  -H "X-Consul-Token: $CONSUL_HTTP_TOKEN" http://127.0.0.1:8500/v1/acl/policy
```

### Create Consul Token

Example `token.json` with `terraform-state` policy assigned.

```json
{
  "Description": "Token for Terraform",
  "Policies": [
    {
      "Name": "terraform-state"
    }
  ],
  "Local": false
}
```

Create example token:

```bash
curl -X PUT --data @token.json \
  -H "X-Consul-Token: $CONSUL_HTTP_TOKEN" http://127.0.0.1:8500/v1/acl/token
```

Set the resulting `secret_id` as `CONSUL_HTTP_TOKEN` environment variable on the machine
where Terraform runs.

### Configure Terraform Backend

Example `backend.tf` with custom path for your module:

```hcl
terraform {
  backend "consul" {
    address = "consul.service.consul:8500"
    lock    = true
    path    = "terraform-state/example/terraform.tfstate"
    scheme  = "http"
  }
}
```

That should do the job!
