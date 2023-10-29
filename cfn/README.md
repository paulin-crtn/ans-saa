# 📝 CloudFormation

CloudFormation is a tool to create, update and delete resources in AWS using a template (`YAML` or `JSON`). What are the components of a template ?

- `Resources`: at least one, each one is called a logical resources (contains a `Type` and `Properties`)
- `Description`: must follow `AWSTemplateFormatVersion` (if present)
- `Metadata`: control what is display in the console/UI
- `Parameters`: what is prompt to the user (for user input or select values)
- `Mappings`: key/value pairs which can be used for lookups
- `Conditions`: create a condition (then we can use it with its name in other components)
- `Outputs`: when the template is finished it can present outputs based on waht has been created, updated or deleted (e.g. ID of the created instance)

> [!NOTE]
> A stack contains all the **logical resources** that the template tells it to contain

> [!IMPORTANT]
> For any logical resources in the stack, CloudFormation makes a corresponding **physical resource** in the AWS account (and keep it in sync: update, delete)

