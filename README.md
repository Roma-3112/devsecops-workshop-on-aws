## DevSecOps on AWS

### Tools and Steps for this Workshop

#### 0. [cfn-validate-template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-validate-template.html)
The aws cloudformation validate-template command is designed to check only the syntax of your template. It does not ensure that the property values that you have specified for a resource are valid for that resource. Nor does it determine the number of resources that will exist when the stack is created.


#### 1. [cfn_nag](https://github.com/stelligent/cfn_nag)
The cfn-nag tool looks for patterns in CloudFormation templates that may indicate insecure infrastructure. Roughly speaking it will look for:

- IAM rules that are too permissive (wildcards)
- Security group rules that are too permissive (wildcards)
- Access logs that aren't enabled
- Encryption that isn't enabled
- Password literals


#### 2. [taskcat](https://github.com/aws-quickstart/taskcat)
**taskcat** is a tool that tests AWS CloudFormation templates. It deploys your AWS CloudFormation template in multiple AWS Regions and generates a report with a pass/fail grade for each region. You can specify the regions and number of Availability Zones you want to include in the test, and pass in parameter values from your AWS CloudFormation template. taskcat is implemented as a Python class that you import, instantiate, and run.

https://aws.amazon.com/blogs/infrastructure-and-automation/up-your-aws-cloudformation-testing-game-using-taskcat/


### 3. [Continuous integration and evaluation of security controls](https://aws.amazon.com/blogs/security/how-to-manage-security-governance-using-devops-methodologies/)
After you write multiple action statements and scenarios supporting the user story (both positive and negative), you can write them up as a runbook, an AWS Config rule, or a combination of both as required.

The second example acceptance criteria above would need to be written as a runbook, as it’s a responsive control. You wouldn’t want to generate a stream of emails to your security operations manager to validate that it’s working.

The other two examples could be written as AWS Config rules using a call to the iam:simulate-custom-policy API, since they are related to preventative controls. An AWS Config rule allows your entire account to be continuously evaluated for compliance, essentially evaluating your control adherence on a <15min basis, rather than from a yearly audit.

Committing those runbook and AWS Config rules to a central code repository fosters the agility of the controls. In the case of runbooks, you may want to adopt a light-weight markup format, such as markdown, that you are able to check in like code. The defined controls can then sit in a CI/CD pipeline, allowing the security controls to be as agile as your pace of innovation.

##### 3.1. [AWS Config](https://aws.amazon.com/config/)
You can use AWS Config as your framework for creating and deploying governance and compliance rules across your AWS accounts and regions. You can codify your compliance requirements as AWS Config rules and author remediation actions using AWS Systems Manager Automation documents and package them together within a conformance pack that can be easily deployed across an organization. Therefore, using AWS Config, you can automate assessment of your resource configurations and resource changes to help you ensure continuous compliance and self-governance across your AWS infrastructure.

##### 3.2. [Amazon Inspector](https://aws.amazon.com/inspector/?nc2=h_ql_prod_se_in)
Amazon Inspector is an API-driven service that analyzes network configurations in your AWS account and uses an optional agent for visibility into your Amazon EC2 instances. This makes it easy for you to build Inspector assessments right into your existing DevOps process, decentralizing and automating vulnerability assessments, and empowering your development and operations teams to make security assessments an integral part of the deployment process.


### [Walkthrough: Building a Pipeline for Test and Production Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-basic-walkthrough.html)
This walkthrough builds a pipeline for a sample WordPress site in a stack. The pipeline is separated into four stages. Each stage must contain at least one action, which is a task the pipeline performs on your artifacts (your input). A stage organizes actions in a pipeline. CodePipeline must complete all actions in a stage before the stage processes new artifacts, for example, if you submitted new input to rerun the pipeline.

By the end of this walkthrough, you'll have a pipeline that performs the following workflow:

1. The first stage of the pipeline retrieves a source artifact (an AWS CloudFormation template) from a repository. You'll prepare an artifact that includes a sample WordPress template and upload it to an S3 bucket.

3. In the second stage, the pipeline performs a series of validation tests to the AWS CloudFormation template. These include cfn-validate-template, cfn_nag and taskcat, and then the pipeline continues to the next stage.

3. In the third stage, the pipeline creates a test stack and then waits for your approval.
After you review the test stack, you can choose to continue with the original pipeline or create and submit another artifact to make changes. If you approve, this stage deletes the test stack, and then the pipeline continues to the next stage.

4. In the fourth stage, the pipeline creates a change set against a production stack, and then waits for your approval.
In your initial run, you won't have a production stack. The change set shows you all of the resources that AWS CloudFormation will create. If you approve, this stage executes the change set and builds your production stack.


## License
This library is licensed under the MIT-0 License. See the LICENSE file.

