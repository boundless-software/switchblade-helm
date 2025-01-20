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
- **Cloud Access**: AWS IAM Role with required permissions
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


*All of these steps can be done in console or CLI*

### 2. Set Up IAM Role for Kubernetes

Switchblade uses **IAM Roles for Service Accounts (IRSA)** to securely access your AWS account without passing static credentials.

#### Step 2.1: Create an IAM Role for Switchblade

1. **Create a combined policy** for Switchblade to manage AWS resources and licenses. Save the policy as `switchblade-combined-policy.json`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "license-manager:CheckoutLicense",
        "license-manager:CheckInLicense",
        "license-manager:ExtendLicenseConsumption",
        "license-manager:GetLicense"
      ],
      "Resource": "*"
    }
  ]
}
```

2. **Create the policy in AWS**:

```bash
aws iam create-policy --policy-name SwitchbladeCombinedPolicy --policy-document file://switchblade-combined-policy.json
```

3. **Create the IAM role** and associate the policy. Ensure you trust the EKS OIDC provider:

```bash
aws iam create-role \
  --role-name SwitchbladeRole \
  --assume-role-policy-document file://<(echo '{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/<EKS_CLUSTER_OIDC_PROVIDER_URL>"
        },
        "Action": "sts:AssumeRoleWithWebIdentity",
        "Condition": {
          "StringEquals": {
            "<EKS_CLUSTER_OIDC_PROVIDER_URL>:sub": "system:serviceaccount:operators:switchblade"
          }
        }
      }
    ]
  }')
```

4. Attach the policy to the role:

```bash
aws iam attach-role-policy --role-name SwitchbladeRole --policy-arn arn:aws:iam::<AWS_ACCOUNT_ID>:policy/SwitchbladeCombinedPolicy
```

5. Associate OIDC Provider with your EKS Cluster.
```
eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster <your-cluster-name> \
  --approve
```

*This setup allows Switchblade to use the IAM role dynamically without requiring static credentials.*

#### Step 2.3: Attach the Policy to an EC2 Instance (Optional for k3s Clusters)

If you are running Switchblade on a k3s cluster on EC2, you can attach the IAM policy directly to the EC2 instance:

1. Navigate to the **IAM Role** section of the AWS Management Console.
2. Create a new IAM role for EC2 and attach the `SwitchbladeCombinedPolicy`.
3. Attach the IAM role to the EC2 instance:

```bash
aws ec2 associate-iam-instance-profile \
  --instance-id <INSTANCE_ID> \
  --iam-instance-profile Name=<IAM_ROLE_NAME>
```

*Ensure the EC2 instance has the necessary permissions to manage AWS resources.*

### 3. Configure the License Key

You can obtain a license key by contacting us [here](https://boundless.software/contact/) and providing your AWS Account ID and Marketplace Agreement Type.

```bash
export LICENSE_KEY=<LICENSE_KEY>
kubectl create secret -n operators generic switchblade \
  --from-literal LICENSE_KEY=$LICENSE_KEY
```

### 4. Create an S3 State Bucket

This bucket will store the operator’s state. You can use the AWS Console or CLI to create it.

```bash
aws s3api create-bucket --acl private --bucket mycompany-myenvironment-switchblade-state
```

*Ensure the bucket is private to safeguard your operator’s state information.*

### 5. Install the Helm Chart

1. Download the values file from Artifact Hub:
   [Switchblade Helm Chart Values](https://artifacthub.io/packages/helm/switchblade/switchblade?modal=values).

2. Save the file as `values.yaml` and update the following fields:
   - `AWS_STATE_BUCKET`: The name of the state bucket created in step 4.
   - `AWS_STATE_BUCKET_REGION`: The region of the state bucket.
   - `SERVICE_ACCOUNT`: The service account created in step 2.2.

3. Install the Helm chart:

```bash
helm repo add switchblade https://www.helm.boundless.software/charts/stable
helm install switchblade -n operators switchblade/switchblade --version 0.0.19 -f values.yaml
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