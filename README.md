# Automating Terraform with GitHub Actions

**Main Points**
* GCS bucket is serving as terraform backend.
* Workflow offers concurrency which means only one workflow can be run at a time; I’ve done this to prevent our terraform state from locking and getting corrupted.
* GitHub secrets are being used to pass GCP Service Account credentials safely on runtime.
* Terraform Plan generates a plan file which is further used by terraform apply. This is done to prevent uninformed changes b/w plan and apply.
* Terraform code must be properly formatted which is considered a good practice else terraform format validation will throw an error and the pipeline would get stopped.
* Manual Approval before applying terraform apply stage.


