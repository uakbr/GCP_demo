## Automating Terraform with GitHub Actions

**Main Points**
* GCS bucket is serving as terraform backend.
* Workflow offers concurrency which means only one workflow can be run at a time; I’ve done this to prevent our terraform state from locking and getting corrupted.
* GitHub secrets are being used to pass GCP Service Account credentials safely on runtime.
* Terraform Plan generates a plan file which is further used by terraform apply. This is done to prevent uninformed changes b/w plan and apply.
* Terraform code must be properly formatted which is considered a good practice else terraform format validation will throw an error and the pipeline would get stopped.
* Manual Approval before applying terraform apply stage.

## How does it work?

* Whenever a developer pushes TF code on GitHub, GA workflow will be triggered and start running necessary TF commands. The workflow is divided into 2 jobs — one dedicated to plan and another one apply.

* Only main branch is allowed to provision infrastructure in the cloud, feature branches are only for terraform plan where you can see desired configurations.

* Before running `terraform plan`, GA will run `terraform init` and `terraform fmt` through the script and before running `terraform apply`, it will run `terraform init` script only. `Tf plan` will generate `tf_plan file/artifact` which will be further used by `tf apply`. `actions/upload-artifact@v2` and `actions/download-artifact@v2` allows uploading (in `tf plan` job) and downloading (in `tf apply` job) or in short **sharing of artifacts b/w jobs**.

## Terraform apply is manual
* As per my knowledge, GitHub Actions does not have manual approval other than the environment way. I searched a lot, but no luck; so I’ve gone with GitHub Environments where I’ve configured `tf_apply` as my environment and **add myself as a reviewer**. I’ve done this just to prevent any infrastructure mess.

```
terraform_apply:
  name: 'Terraform Apply'
  runs-on: ubuntu-latest
  needs: terraform_plan
  if: github.ref == 'refs/heads/main' && github.event_name == 'push'
  environment:
    name: tf_apply
```
