# ☁️ Virtual Private Cloud

A Virtual Network inside AWS.

- A VPC is within 1 account & 1 region (regionally resilient)
- Private and isolated: only services deployed into the same VPC can communicate (unless decide otherwise)
- 2 types: `Default VPC` and `Custom VPCs`
- VPC CIDR: start and end range of IP addresses that a VPC can use to communicate
- VPC can subdivide into subnets (each subnet represents an AZ, set at the creation and cannot be changed): RESILIENCE 🙌
- Each subnet uses part of the VPC's IP address range (cannot overlap with each other)

## 🥸 Default VPC Facts

Pre-configured by AWS and less customizable.

- 1 per region only (can be removed and recreated)
- Default VPC CIDR is always `172.31.0.0/16`
- Already configured to have one subnet in each AZ: e.g. `172.31.0.0/20`, `172.31.16.0/20` and `172.31.32.0/20` for 3 AZ
- Provided with Internet Gateway, Security Group & NACL
- Subnets assign public `IPv4` addresses
