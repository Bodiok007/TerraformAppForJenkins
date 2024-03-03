# TerraformForJenkins

## terraform.Jenkinsfile pipeline structure

1. `Checkout` - clears workspace and pulls files from repository.
2. `Verify Terraform Installation` - verifies if terraform installed.
3. `Init` - initializes terraform.
4. `Plan` - calls terraform plan.
5. `Apply` - creates infrustructure.
6. `Destroy` - destroys infrustructure.
7. Parameters:
- `apply` - performs `Apply` stage operation if checked
- `destroy` - performs `Destroy` stage operation if checked
