# Install Terraform

```
brew install terraform
terraform init
```

Note -- We probably used `chronotf` somewhere in this setup, but not sure.

# About terraform file
Terraform files have resources. Resources are defined, and then terraform picks it up.
For example -- A dashboard is a resource. Each dashboard has number of thigns that need to be added.
1. `uid` is unique to each dashboard. CHange it when copy/pasting, otherwise `terraform apply` will fail.
2. Change the name and id of the resource. This has to be unique.
3. `bucket` is probably an older artifact
4. Dashboard is made of panels. Panels is a JSON array.
5. The dashboard JSON has to go between `EOF` tags. This is because `main.tf` is a HCL language file. However, anything between `EOF` tags makes it clear that it is JSON and not HCL.

# Apply changes
```
terraform apply
```