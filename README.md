# Terraform scripts for Infrastructure Configuration of an S3 Hosted Static Content Site

This is a set of Terraform script files to set up a static website in AWS. It makes use of the following resources:

- S3
- Route53
- CloudFront
- Certificate Manager

The end result is a website that supports:

- A primary and secondary domain (typically a root domain and then the wildcard verison of the domain)
- HTTPS

## Usage

1. Install Terraform
1. Configure your AWS profile
1. `git clone git@github.com:mjolnirmkii/terraform-s3-static-site-config.git`
1. `cd terraform-s3-static-site-config`
1. `terraform plan`
1. `terraform apply`

You can create a `terraform.tfvars` file containing your specific values like the below:

```
primary_domain = "example.com"
secondary_domain = "*.example.com"
access_key_val = "adsfasdfasdfasdf"
secret_key_val = "12o89puafskljfaspiu8ui8opr42"
```

You will be prompted to provide values via the command line if a tfvars file is not present.


If AWS is not your domain registrar, you will need to set your domain's name servers
to AWS name servers associated with your hosted zone. Terraform will output those
automatically like the following:

```
Outputs:

name_servers = [
    ns-1500.awsdns-59.org,
    ns-1595.awsdns-07.co.uk,
    ns-619.awsdns-13.net,
    ns-63.awsdns-07.com
]
```

Once this is complete, you can host your static content site within your newly created S3 bucket. Remember that the CloudFront distribution refresh is every 24 hours by default, so any changes will not get picked up and displayed publically until the CloudFront distribution caches are refreshed. You can either wait this amount of time or go into the AWS console and invalidate the S3 bucket contents so the caches are refreshed or change the default TTL values to something lower than a 24 hour interval. My preference is usually 12 hours.

(AWS can encounter errors in the creation of the Certificate Manager new SSL certificate. If this is the case, Terraform may time out. If this is the case, you should be able to re-run this again by removing the ssl-certificate.tf file to skip the certificate creation step.)
