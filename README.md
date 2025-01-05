# Welcome to Switchblade!

Switchblade is a powerful Kubernetes operator for deploying and managing cloud infrastructure and Helm charts effortlessly. This guide provides installation instructions, update procedures, and common scenarios. If you have questions, please don’t hesitate to contact us.

---

## Documentation and Support

- Documentation with examples: [Switchblade Documentation](http://switchblade-samples.boundless.software/aws/)
- Support Email: [support@boundless.software](mailto:support@boundless.software)

---

## Requirements

To install and use Switchblade, ensure you meet the following requirements:

- **Architecture**: x86_64 platform
- **Cloud Access**: AWS credentials (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`)
- **Cluster Access**: Kubernetes access
- **License**: Switchblade license key
- **Tooling**: Helm 3.0+

---

## Installation Steps

### 1. Create a Namespace for Switchblade

```bash
kubectl create ns operators
```

*Creating a separate namespace ensures isolation of resources specific to Switchblade.*

### 2. Configure AWS Credentials and License Key

#### Set up AWS Credentials

The access key user must have **Admin permissions** on the AWS account. These credentials will be used to manage resources.

```bash
export AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID>
export AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY>
kubectl create secret -n operators generic aws \
  --from-literal AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
  --from-literal AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
```

*It is recommended to use a secure secrets management solution for production environments.*

#### Set up License Key

You can obtain a license key by contacting us [here](https://boundless.software/contact/) and providing your AWS Account ID and Marketplace Agreement Type.

```bash
export LICENSE_KEY=<LICENSE_KEY>
kubectl create secret -n operators generic switchblade \
  --from-literal LICENSE_KEY=$LICENSE_KEY
```

### 3. Create an S3 State Bucket

This bucket will store the operator’s state. You can use the AWS Console or CLI to create it.

```bash
aws s3api create-bucket --acl private --bucket mycompany-myenvironment-switchblade-state
```

*Ensure the bucket is private to safeguard your operator’s state information.*

### 4. Install the Helm Chart

1. Download the values file from Artifact Hub:
   [Switchblade Helm Chart Values](https://artifacthub.io/packages/helm/switchblade/switchblade?modal=values).

2. Save the file as `values.yaml` and update the following fields:
   - `AWS_STATE_BUCKET`: The name of the state bucket created in step 3.
   - `AWS_STATE_BUCKET_REGION`: The region of the state bucket.

3. Install the Helm chart:
   - helm chart can be found here: https://artifacthub.io/packages/helm/switchblade/switchblade

```bash
helm repo add switchblade https://artifacthub.io/packages/helm/switchblade
helm install switchblade switchblade/switchblade --version 0.0.19 -f values.yaml
```

*Verify the Helm repository to ensure it is trusted and secure before proceeding.*

---

## Updating the Helm Chart

To update the chart to a newer version, use the `helm upgrade` command with your updated `values.yaml` file:

```bash
helm upgrade switchblade switchblade/switchblade --version <NEW_VERSION> -f values.yaml
```

---

## Uninstalling the Helm Chart

To uninstall Switchblade, run the following command:

```bash
helm uninstall switchblade
```

---

## Contact Us

For further assistance or inquiries, feel free to reach out:
- Email: [support@boundless.software](mailto:support@boundless.software)
- Documentation: [Switchblade Documentation](http://switchblade-samples.boundless.software/aws/)