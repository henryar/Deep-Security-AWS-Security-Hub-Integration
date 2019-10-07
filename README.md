# Project Retired

Thank you for your interest in this integration between Trend Micro’s Deep Security and [AWS Security Hub](https://aws.amazon.com/security-hub/). This integration was created early in the product lifecycle of AWS Security Hub and was made available during the preview phase of the service.

During the preview phase, this integration was tested in a number of scenarios with an assortment of customers. Based on those experiences, we’re retiring this integration as we look at ways to make the overall experience smoother and the focus on generating actionable findings.

If you have a use case you’d like to highlight to the team or feedback on these efforts, please email aws@trendmicro.com.

## Trend Micro Deep Security AWS Security Hub Integration

Send Deep Security security events to AWS Security Hub.

## AWS Configuration
### Organizations (optional)

1. Create an organization account for an employee.
2. Create policy & assume role as per these [instructions.](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access.html#orgs_manage_accounts_access-cross-account-role)

### Security Hub

1. Browse to the Security Hub page in your account.
4. Click **"Integrations"** -> enable **"Deep Security"**

Note: If you're using multiple accounts, you will need to configure your [Master and Member accounts.](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-accounts.html)

### SNS & IAM

1. Create a new SNS topic.
2. Create a new IAM account with programmatic access.
3. Give the account `sns:publish` access to the newly created SNS topic.

### Lambda & IAM

1. Create a Lambda with `basic Lambda permissions`.
2. Add rule to allow `BatchImportFindings`.

## Deep Security

1. Set up SNS as per [these instructions.](https://www.trendmicro.com/aws/sending-security-event-data-amazon-sns/)
2. Create a policy with the desired options enabled.
3. Enable the [AWS connector.](https://help.deepsecurity.trendmicro.com/Add-Computers/add-aws.html)

### Test Deep Security Connectivity (optional)

1. Create a basic Lambda script, like so:
    ```
    import json

    def lambda_handler(event, context):
        print('Received SNS notification. Payload will be printed below:')
        print(json.dumps(event))
    ```
2. Subscribe the Lambda to the SNS topic.
3. From within Deep Security, run a DS SNS topic test.
4. Check the Lambda's CloudWatch logs to ensure the test was successful.

### Lambda

1. Use pip to download the latest version of boto3 and put it into a directory called `python`:

    ```
    cd /tmp
    sudo pip3 install --target=/tmp/python boto3
    zip -r boto3.zip python/
    ```
	
	Note: For convenience, this has already been done and can be found in `lambda/boto3.zip`. 

2. Upload the zip file as a Lambda layer.
3. Add the layer to the newly created Lambda.
4. Copy and paste the contents of `lambda/ds-sec-hub.py` script into the above mentioned Lambda.

## Integration Test (optional)

1. Spin up a Windows instance.
2. Install the Deep Security agent.
3. Reboot in order to enable anti-malware features.
4. Create a text file on the desktop. Paste the following [EICAR string](https://www.eicar.org/?page_id=3950) and save the file:

	```
	X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
	``` 

5. Check Security Hub for an entry.

## Troubleshooting

If no entries appear in Security Hub, check the Lambda's log.

## Credit

This is a re-write of an [existing script.](https://github.com/deep-security/aws-security-hub) This version includes additonal features and logging outputs.
