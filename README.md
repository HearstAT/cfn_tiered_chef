# ha-chef-stack

Cloudformation Templates to setup our Tiered Chef Stack

This requires the use of [cf_tiered_chef](https://github.com/HearstAT/cf_tiered_chef) cookbook to function.

This is a nested template setup that installs Frontends, and a Backend. Also puts data in-place to support a standalone analytics server. Assumse chef-analytics.$hostedzone is the domain of the server.

## Info
* Builds out Tiered Setup; [documentation](https://docs.chef.io/install_server_ha_aws.html)
* Built to utilize Ubuntu Trusty
* Uses Chef APT Repo for packages (Updated to the new repo)

## What's Setup
Please see the wiki [here](https://github.com/HearstAT/cfn_tiered_chef/wiki/Build-Steps-Process) for more specific info
* Backend with Secondary EBS
* 2 Front Ends with a Stage file to allow setting up a secondary Subdomain/Domain for Blue/Green Deployments
* All the items required by from the Backend Server will be generated first during that config, then distributed via S3 bucket.

## Requirements
Please see the wiki [here](https://github.com/HearstAT/cfn_tiered_chef/wiki/Prerequisites) for more specific info
* Existing VPC
  * IP Scheme (To create static VIP)
  * SSH Security Group (Will lookup existing groups in AWS, make sure one exists)
* Route53 Hosted Domain/Zone
* Existing SSL Certificate (Loaded into AWS and provide in the params below)

## Parameters
Please see the wiki [here](https://github.com/HearstAT/cfn_tiered_chef/wiki/Parameters) for more specific info
* Instance & Network Configuration
    * BackendInstanceType
    * FrontendInstanceType
    * KeyName
    * SSLCertificateARN (See [here](http://docs.aws.amazon.com/cli/latest/reference/iam/index.html#cli-aws-iam) on how to get the Cert ARN)
      * `aws iam get-server-certificate --server-certificate-name`
    * VPC
    * SSHSecurityGroup
    * AvailabilityZoneA
    * AvailabilityZoneB
    * HostedZone
    * BackendVIP
* Stack Configuration
    * BackendTemplateURL (URL for the chef_backends_nested.json in this repo)
    * FrontendTemplateURL (URL for the chef_frontends_nested.json in this repo)
* Bucket Configuration
    * UseExistingBucket (True/False, enables using a bucket rather than creating one. Good for re-deploys)
    * ExistingBucket (Bucket Name for the bucket you want to use)
* Re-Deploy Configuration
    * ExistingInstall (True/False, will touch bootstrapped file and pull everything necessary when using an external DB)
    * ExistingIAM (True/False, select to use a previous IAM user rather than creating one)
    * IAMUser (If above true)
    * AccessKey (If above true)
    * SecretKey (If above true)
* Chef Configuration
    * ChefSubdomain
    * SignupDisable (True/False)
    * SupportEmail (Optional)
    * LicenseCount (Optional, default 25)
    * ChefDir (location for cookbook items)
    * S3DIR (Location to mount created S3 bucket for citadel items and chef backups, i.e.; /opt/chef-s3)
    * BackupEnable (True/False Option, enables a backup script that will run `knife ec` daily and copy to a S3 bucket, keeps only 10 days worth)
    * RestoreFile (optional)
* Mail Configuration (Optional)
    * MailCreds (Setup to noecho throught template)
    * MailHost (Optional)
    * MailPort (Optional)
* New Relic Configuration (Optional)
    * NewRelicEnable (Choose true/false)
    * NewRelicAppName (Name that shows up in New Relic APM)
    * NewRelicLicense (Setup to noecho throught template)
* Sumologic Configuration (Optional)
    * SumologicEnable (Choose true/false)
    * SumologicAccessID (Setup to noecho throught template)
    * SumologicAccessKey (Setup to noecho throught template)
    * SumologicPassword (Setup to noecho throught template)
* External Build Items
    * Cookbook (name of cookbook)
    * CookbookGit (URL for [cf_tiered_chef](https://github.com/HearstAT/cf_tiered_chef))
    * CookbookGitBranch (if using something different from master)
    * UserDataScript (URL for the `userdata.sh` script in this repo)
