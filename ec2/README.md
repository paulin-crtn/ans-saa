# 💥 Elastic Compute Cloud

IAAS - Provides Virtual Machines (Instances).

- Private service by default - uses VPC networking
- AZ resilient: instance fails if AZ fails
- Different instance sizes and capabilities
- On-demand billing - per second
- Local on-host storage or Elastic Block Store (`EBS`)

> [!NOTE]
> Connect to EC2: private key is send via `SSH` and public key is stored on the instance

## 🔄 Instance Lifecycle

- `Running` - Consume compute, ram, storage and network resources
- `Stopped` - Can be restarted, still consume/bill EBS storage
- `Terminated` - Cannot be restarted, no cost

## 🌇 Amazon Machine Image

AMI can be used to create an EC2 instance or can be created from an EC2 instance. An AMI contains:

- Permissions: **Public Access**, **Owner** (implicit allow), **Explicit** (specific AWS accounts allowed)
- Root Volume: what boot the volume system
- Block Device Mapping: determines which volume is the boot volume and which volume is the data volume

> [!NOTE]
> **Instance** and **Network** settings are not stored in an AMI
