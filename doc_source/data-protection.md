# Data Protection in VM Import/Export<a name="data-protection"></a>

 VM Import/Export conforms to the AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/), which includes regulations and guidelines for data protection\. AWS is responsible for protecting the global infrastructure that runs all the AWS services\. AWS maintains control over data hosted on this infrastructure, including the security configuration controls for handling customer content and personal data\. AWS customers and APN partners, acting either as data controllers or data processors, are responsible for any personal data that they put in the AWS Cloud\. 

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\), so that each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use TLS to communicate with AWS resources\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields or metadata, such as function names and tags\. Any data that you enter into metadata might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

For more information about data protection, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

## Encryption at Rest<a name="encryption-rest"></a>

 VM Import/Export does not store your data at rest\.

## Encryption in Transit<a name="encryption-transit"></a>

 VM Import/Export encrypts your data while performing import tasks\. To ensure that the destination AMI or snapshot is encrypted, specify the `--encrypted` parameter when you call the [import\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) or [import\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-snapshot.html) command\.

When performing an import task, VM Import/Export stores data temporarily in an intermediate EBS volume\. Each task gets a separate EBS volume\. When an import task is completed, VM Import/Export deletes its intermediate EBS volume\.