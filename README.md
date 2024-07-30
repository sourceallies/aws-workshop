# aws-workshop
Technical workshop instructions and information.

## Machine Setup Checklist
- [ ] python: https://www.python.org/downloads/. Verify by running `python --version`. Should be a 3.x version.
- [ ] aws-cli: get installer and run https://aws.amazon.com/cli/, or use pip `pip install awscli`. Verify by running `aws --version`
- [ ] sam-cli: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html. Verify by running `sam --version`
- [ ] an editor you like
- [ ] authentication to AWS.
        - Go to the Source Allies AWS SSO page that lists our AWS accounts at https://allies.awsapps.com/start/#/?tab=accounts
        - We'll be using the Source Allies Dev account. Expand that account and click the "Access Keys" link. Then use Option 1 to access key, secret, and session token into your terminal (shell).
        - Verify by running `aws s3 ls`, which will list all s3 buckets in the account.


