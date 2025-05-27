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

3. Add the Helm repository:
```bash
helm repo add switchblade https://www.helm.boundless.software/charts/stable
```

4. Install Switchblade:
```bash
helm install switchblade -n operators switchblade/switchblade --version 0.0.19 -f values.yaml --create-namespace
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