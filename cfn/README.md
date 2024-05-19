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
