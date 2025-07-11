# Welcome to Switchblade!

Switchblade is a powerful Kubernetes operator for deploying and managing cloud infrastructure and Helm charts effortlessly. This guide provides installation instructions, update procedures, and common scenarios.

## Quick Links

- [Documentation](https://www.boundless.software/docs/introduction/getting-started)
- [Support](mailto:support@boundless.software)
- [Helm Values](https://www.helm.boundless.software/values.yaml)
- [CloudFormation Template](https://www.helm.boundless.software/switchblade-cf-stack.yaml)

## Requirements

- **Architecture**: x86_64 platform
- **Cloud Access**: AWS IAM Role with required permissions
- **Cluster Access**: Kubernetes access
- **Tooling**: Helm 3.0+

## Installation

1. Deploy the CloudFormation stack using the template from [switchblade-cf-stack.yaml](https://www.helm.boundless.software/switchblade-cf-stack.yaml)
   - The stack will create:
     - IAM Role for Switchblade
     - S3 bucket for state management
     - All necessary permissions

2. Download the values file:
```bash
curl -o values.yaml https://www.helm.boundless.software/values.yaml
```

3. Update the following values in `values.yaml` (replace with your values):
```yaml
env:
  - name: AWS_STATE_BUCKET
    value: "my-company-switchblade-state"  # Replace with your S3 bucket name
  - name: AWS_STATE_BUCKET_REGION
    value: "us-east-1"  # Replace with your bucket region

serviceAccount:
  annotations:
    eks.amazonaws.com/role-arn: "arn:aws:iam::123456789012:role/SwitchbladeRole"  # Replace with your IAM role ARN
```

4. Add the Helm repository:
```bash
helm repo add switchblade https://www.helm.boundless.software/charts/stable
```

5. Install Switchblade (choose one method):

Using values.yaml:
```bash
helm install switchblade -n operators switchblade/switchblade --version 0.0.19 -f values.yaml --create-namespace
```

Or using --set flags:
```bash
helm install switchblade -n operators switchblade/switchblade --version 0.0.19 \
  --set env[0].name=AWS_STATE_BUCKET \
  --set env[0].value=<my-company-switchblade-state> \
  --set env[1].name=AWS_STATE_BUCKET_REGION \
  --set env[1].value=us-east-1 \
  --set serviceAccount.annotations."eks\.amazonaws\.com/role-arn"=<my-company-switchblade-role> \
  --create-namespace
```

## Updating

To update to a newer version:

```bash
helm upgrade switchblade switchblade/switchblade --version <NEW_VERSION> -f values.yaml
```

## Uninstalling

```bash
helm uninstall switchblade -n operators
helm repo remove switchblade
```

## Support

For assistance or inquiries:
- Email: [support@boundless.software](mailto:support@boundless.software)
- Documentation: [Switchblade Documentation](http://switchblade-samples.boundless.software/aws/)