# Description

This module creates a VM that acts as a login node to test and submit Google
Cloud Batch jobs. It is intended to be used along with the `cloud-batch-job`
module.

This login node:

- Uses the same VM settings as the `cloud-batch-job`, such as image, machine type, etc...
- Has the same mounted file systems as the `cloud-batch-job`.
- Runs the same `startup-script` as the `cloud-batch-job`.
- Contains a folder with the job template generated by the `cloud-batch-job`.

Since the login node has the same mounted storage and is a homogeneous machine
to the Google Cloud Batch compute VMs, it can be used to inspect shared file
systems and test installed software before submitting a Google Cloud Batch job.

## Example

```yaml
- id: batch-job
  source: community/modules/scheduler/cloud-batch-job
  kind: terraform
  ...
  
- id: batch-login
  source: community/modules/scheduler/cloud-batch-login-node
  kind: terraform
  use: [batch-job]
  outputs: [instructions]
```

## Authentication

To submit jobs from the login node, the service account attached to the VM needs
the `Batch Job Administrator` role. In most cases this service account will be
the Compute Engine default service account and will not be granted this role by
default.

You can grant this role either by adding the `Batch Job Administrator` role to
the service account in the IAM page in the Google Cloud Console, or by running
the following command line:

```bash
gcloud projects add-iam-policy-binding <project ID> \
  --member=serviceAccount:<service account email> \
  --role=roles/batch.jobsAdmin
```

## gcloud Batch Access

Until the Google Cloud Batch API is generally available (GA), it may not be
available in all versions of the `gcloud` cli. You can test if the Google Cloud
Batch commands are available by running `gcloud [alpha|beta|] batch -h`. If the
Google Cloud Batch cli is not available it can generally be mitigated by either
updating `gcloud` by running `gcloud components update`, or using an image that
contains a more recent version of `gcloud`.

> _**WARNING:**_ It is a known issue that the version of `gcloud` on the
> [HPC VM image](https://cloud.google.com/compute/docs/instances/create-hpc-vm)
> does not contain the Google Cloud Batch cli.

## License

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
Copyright 2022 Google LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.14.0 |
| <a name="requirement_google"></a> [google](#requirement\_google) | >= 3.83 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_google"></a> [google](#provider\_google) | >= 3.83 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_login_startup_script"></a> [login\_startup\_script](#module\_login\_startup\_script) | github.com/GoogleCloudPlatform/hpc-toolkit//modules/scripts/startup-script | v1.0.0 |

## Resources

| Name | Type |
|------|------|
| [google_compute_instance_from_template.batch_login](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/compute_instance_from_template) | resource |
| [google_compute_instance_template.batch_instance_template](https://registry.terraform.io/providers/hashicorp/google/latest/docs/data-sources/compute_instance_template) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_batch_job_directory"></a> [batch\_job\_directory](#input\_batch\_job\_directory) | The path of the directory on the login node in which to place the Google Cloud Batch job template | `string` | `"/home/batch-jobs"` | no |
| <a name="input_deployment_name"></a> [deployment\_name](#input\_deployment\_name) | Name of the deployment, also used for the job\_id | `string` | n/a | yes |
| <a name="input_enable_oslogin"></a> [enable\_oslogin](#input\_enable\_oslogin) | Enable or Disable OS Login with "ENABLE" or "DISABLE". Set to "INHERIT" to inherit project OS Login setting. | `string` | `"ENABLE"` | no |
| <a name="input_gcloud_version"></a> [gcloud\_version](#input\_gcloud\_version) | The version of the gcloud cli being used. Used for output instructions. Valid inputs are `"alpha"`, `"beta"` and "" (empty string for default version). Typically supplied by a cloud-batch-job module. | `string` | `"alpha"` | no |
| <a name="input_instance_template"></a> [instance\_template](#input\_instance\_template) | Login VM instance template self-link. Typically supplied by a cloud-batch-job module. | `string` | n/a | yes |
| <a name="input_job_filename"></a> [job\_filename](#input\_job\_filename) | The filename of the generated job template file. Typically supplied by a cloud-batch-job module. | `string` | n/a | yes |
| <a name="input_job_id"></a> [job\_id](#input\_job\_id) | The ID for the Google Cloud Batch job. Typically supplied by a cloud-batch-job module for use in the output instructions. | `string` | n/a | yes |
| <a name="input_job_template_contents"></a> [job\_template\_contents](#input\_job\_template\_contents) | The contents of the Google Cloud Batch job template. Typically supplied by a cloud-batch-job module. | `string` | n/a | yes |
| <a name="input_labels"></a> [labels](#input\_labels) | Labels to add to the login node. List key, value pairs | `any` | n/a | yes |
| <a name="input_project_id"></a> [project\_id](#input\_project\_id) | Project in which the HPC deployment will be created | `string` | n/a | yes |
| <a name="input_region"></a> [region](#input\_region) | The region in which to create the login node | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_instructions"></a> [instructions](#output\_instructions) | Instructions for accessing the login node and submitting Google Cloud Batch jobs |
| <a name="output_login_node_name"></a> [login\_node\_name](#output\_login\_node\_name) | Name of the created VM |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
