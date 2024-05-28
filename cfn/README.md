# 📝 CloudFormation

CloudFormation is a tool to create, update and delete resources in AWS using a **Template** written in `YAML` or `JSON`.

## 🧩 Components of a Template

- `Resources`: at least one, each one is called a logical resources (contains a `Type` and `Properties`)
- `Description`: must follow `AWSTemplateFormatVersion` (if present)
- `Metadata`: control what is display in the console/UI
- `Parameters`: what is prompt to the user (for user input or select values)
- `Mappings`: key/value pairs which can be used for lookups
- `Conditions`: create a condition (then we can use it with its name in other components)
- `Outputs`: when the template is finished it can present outputs based on waht has been created, updated or deleted (e.g. ID of the created instance)

## 🏗️ Physical and Logical Resources

CF **Template** are used to create one or multiple **Stacks**.

- A stack creates/updates/deletes **Physical resources** based on the **Logical resources** described in the template
- **Logical resources** have a `Name`, a `Type` (e.g. `AWS::EC2::Instance`) and various `Properties`.

> [!NOTE]
> For any **logical resources** in the stack, CloudFormation makes a corresponding **physical resource** in the AWS account (and keep it in sync: update, delete)

> [!IMPORTANT]
> Once the physical resource is created, its attributes can be queried within the template.

## 🕹️ Template and Pseudo Parameters

### 👾 Template Parameters

- Accept **inputs** (via the Console, CLI or API) when a stack is created or updated (e.g. the environment that the template is for: `dev`, `test`, `prod`)
- Can be **referenced** from within **Logical Resources** ➡️ Influence **Physical Resources** and/or configuration
- Can be configured with `Defaults`, `AllowedValues`, `Min` and `Max` length, `AllowedPattern`, `NoEcho` & `Type`

### 👾 Pseudo Parameters

- Provided by AWS: they exist even if you don't define them in the parameter section of the template (it's like they are injected by AWS into the template and stack) - Can be referenced wherever needed
- E.g. `AWS:Region`, `AWS:StackId`, `AWS:StackName`, `AWS:AccountId`

## ⚙️ Intrinsic Functions

Gain access to data at runtime: template can take actions based on how things are when the template is being used to create a stack.

- `Ref` & `Fn::GetAtt` allow to reference a value from one logical resource (or parameter) in another one
- `Fn::Join` & `Fn::Split` allow to join strings together or split them
- `Fn::GetAZs` can be used to get a list a AZ for a given AWS region
- `Fn::Select` allow to select one element from a list
- `Fn::If`, `Fn::And`, `Fn::Equals`, `Fn::Not`, `Fn::Or` can be used to provision resources based on conditional checks (e.g. if a parameter is set to prod then deploy a bigger instance)
- `Fn::Base64` allow to provide base64 encoded text to a resource
- `Fn::Sub` allow to substitute text based on runtime information
- `Fn::Cidr` allow to automatically configure the network ranges subnets
- `Fn::ImportValue`, `Fn::FindInMap`, `Fn::Transform`

## 🔗 Mappings

**Improve Template Portability**.

- Templates can contain a Mappings object... which can contain many mappings
- Map keys to values, allowing lookup (can have one `Key` or `Top` & `Second` level)
- Mappings use the `Fn::FindInMap` intrinsic function
- Common use: retrieve AMI for given region & architecture

## 🔊 Outputs

Useful to provide status information or showing how to access services which are created by a CloudFormation stack.

- Optional
- Visible as outputs when using the CLI or in the console UI
- Accessible from a **parent stack** when using **nesting**

## 🔀 Conditions

- Processed **BEFORE** resources are created
- Use the other intrinsic functions **AND, EQUALS, IF, NOT, OR**
- Can be associated with logical resources to control if they are created or not (e.g. how many AZs are already created)
- Conditions can be nested

## 🤝 DependsOn

- CloudFormation tries to be efficient by doing things in parrallel (create, update & delete)
- However, it will try to determine a **dependency order** (e.g. VPC ➡️ SUBNET ➡️ EC2)
- **DependsOn** allows to explicitly define any dependencies between resources

> [!NOTE]
> For example, if resource B depends on resource A, then CF will wait until resource A is complete before starting to create resource B.

## 🚦 Wait Conditions & Signal

A stack can be in **CREATE_COMPLETE** state but a resource behind it can fail (e.g. an EC2 instance).

- `cfn-signal` wait for **X number of success signal** and/or wait for a **timeout** (12 hour max) before indicating the **CREATE_COMPLETE** state (if failures signal is received or timeout is reached then the creation fails)
- Both `WaitCondition` & `CreationPolicy` delay the creation of the stack until they receive a specified number of "success signals"
- `WaitCondition` is a CloudFormation **RESOURCE** in itself, whereas `CreationPolicy` is an **ATTRIBUTE** associated with other resources
- Currently, only `AutoScalingGroup`, `EC2` Instance & `WaitCondition` resources support the `CreationPolicy` attribute

## 🧱 Nested Stacks & Cross-Stack References

- Resources in a single stack **share a lifecycle**
- A stack can have a maxmum of `500` resources
- A stack is **isolated** and **self-contained** by design: can't easily reference or reuse resources from another stack ➡️ **Nested Stacks** or **Cross-Stack References** can help

### 🪆 Nested Stacks

- **Root Stack** is created first
- **Parent Stack** contains multiple stacks (its own nested stack)
- Useful for modular templates (code reuse)

> [!IMPORTANT]
> Can only reference **OUTPUTS**, cannot directly reference logical resources

🎒 **Exams**

- Wholes templates can be reused in other stacks (will create new and distinct resources)
- Use only when everything is **lifecycle linked** (when the stacks form part of one solution)

### ↕️ Cross-Stack References

Useful when we want a stack to be able to reference the resources created in another (in order to reuse those actual resources).

- Outputs are normally not visible from other stacks: cannot use the built-in `Ref` function (except for nested stacks)
- Outputs can be **EXPORTED**, making them visible from other stacks
- Exports must have a **UNIQUE name** in the region
- `Fn::ImportValue` can be used instead of `Ref`
- Use for Service-Oriented & different lifecycles & Stack Reuse

## 🌎 StackSets

Deploy CFN stacks across many accounts & regions.

- StackSets are **CONTAINERS** in an **admin account**
- Contain stack **INSTANCES** (❗not the same thing as stack, more like a reference to a stack)
- Stack instances & stacks are in **target accounts**
- Each stack = 1 region in 1 account
- Security: **self-managed** or **service-managed**

## 🧽 Deletion Policy

- If you delete a logical resource from a template then the physical resource is deleted: this can cause data loss
- With deletion policy we can define on each resource: `Delete` (default), `Retain` (physical resources remain untouched when the Stacks logical resources are removed) or `Snapshot` (if supported)
- Snapshots continue on past Stack lifetime (need to clean up $$)

> [!IMPORTANT]
> ONLY APPLIES TO DELETE, NOT REPLACE.

## 🧢 Stack Roles

- CFN uses the permissions of the **logged in identity**: it means that the identity need permissions to create, update or delete stacks or resources
- This can be a problem for large team since the dev updating the stack is not the same as the one who created it
- To address this issue CFN can **assume a role** (role is attached to the stack) to **gain the permissions**
- The identity creating the stack does not need resource permissions - only `PassRole`
- This lets us implement **role separation**

## ✨ CloudFormation Init

Simple configuration management system: helper script installed on **EC2 instance**.

- Configuration directives stored in the template via the `AWS::CloudFormation::Init` attribute inside the logical resource
- **Procedural** - HOW (user data) vs. **Desired State** - WHAT (`cfn-init`)
