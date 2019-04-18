Hosting a Static Website using Amazon S3
======

Introduction
------------
Our goal is learn more about Amazon Web Services. This instruction will host a static website on Amazon S3.
Azure Cloud and Google Cloud Platform also offer the same storage technology which can host website using storage.
We can also maximize Amazon's "Free Tier" for this project.

### What is a Static Website?
A static website contains Web pages with fixed content. Each page is coded in HTML and displays the same information to every visitor. Static sites are the most basic type of website and are the easiest to create. Unlike dynamic websites, they do not require any Web programming or database design. A static site can be built by simply creating a few HTML pages and publishing them to a Web server.

### What Is Amazon S3 (Simple Storage Service)?
Amazon Simple Storage Service is storage for the Internet. It is designed to make web-scale computing easier for developers.

Amazon S3 has a simple web services interface that you can use to store and retrieve any amount of data, at any time, from anywhere on the web. It gives any developer access to the same highly scalable, reliable, fast, inexpensive data storage infrastructure that Amazon uses to run its own global network of web sites. The service aims to maximize benefits of scale and to pass those benefits on to developers.

### Free Tier 
Explore more than 60 products (including Amazon S3) and start building on AWS using the free tier. Three different types of free offers are available depending on the product used. See below for details on each product.
For more info: https://aws.amazon.com/free/

### Static Website using Amazon S3 Architecture
Today we will focus on routing internet traffic to the Amazon S3 bucket. Please ignore the CloudFront in the image below. We will perfom that someday.
![AWS_StaticWebsiteHosting_Architecture](https://github.com/ripples3/Static-Website-using-Amazon-S3/blob/master/AWS_StaticWebsiteHosting_Architecture.png)

### 1. Naming Your S3 Bucket
It is very important that your bucket has the same name as you domain name.
For example, your website domain is "example.com", then your S3 bucket name should be "example.com".

### 2. Configuring Your S3 Bucket for Static Website Hosting
Does your bucket and website has the same domain name? If yes, Let proceed the onto the next steep.
Follow steps below to turn on static website hosting for you S3
- Navigate to S3 in the AWS Console
- Click into your bucket
- Click the “Properties” section
- Click the “Static website hosting” option
- Select “Use this bucket to host a website”
- Enter “index.html” as the Index document

Your bucket is configured for static website hosting, and you now have an S3 website url like this `http://example.com.s3-website-us-east-1.amazonaws.com/`.

Your bucket serves your static website, so it must be accessible to anyone in the world. This is referred to as anonymous access to the bucket.

By default, any new buckets created in an AWS account deny you the ability to add a public access bucket policy. This is in response to the recent leaky buckets where private information has been exposed to bad actors. However, for our use case, we need a public access bucket policy. To allow this you must complete the following steps before adding your bucket policy.

- Click into your bucket.
- Select the “Permissions” tab at the top.
- Under “Public Access Settings” we want to click “Edit”.
- Change “Block new public bucket policies”, “Block public and cross-account access if bucket has public policies”, and “Block new public ACLs and uploading public objects” to be false and Save.

You must complete this step before adding the bucket policy to your static website bucket.

Now you must update the Bucket Policy of your bucket to have public read access to anyone in the world. The steps to update the policy of your bucket in the AWS Console are as follows:

- Navigate to S3 in the AWS Console.
- Click into your bucket.
- Click the “Permissions” section.
- Select “Bucket Policy”.
- Add the following Bucket Policy and then Save

```
{
    "Version": "2008-10-17",
    "Id": "PolicyForPublicWebsiteContent",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::example.com/*"
        }
    ]
}
```

### 3. Adding A CNAME Record For Your Bucket Url
We are almost done!!! Now that your S3 bucket is accessible anywhere around the world.

In order for a user to load your S3 website you’ll need to provide mapping from your domain name example.com, to your S3 website url example.com.s3-website-us-east-1.amazonaws.com. This mapping is often referred to as a CNAME record inside of your Domain Name Servers (DNS) records.

To route traffic to an S3 bucket

Sign in to the AWS Management Console and open the Route 53 console at https://console.aws.amazon.com/route53/.

In the navigation pane, choose Hosted Zones.

Choose the name of the hosted zone that has the domain name that you want to use to route traffic to your S3 bucket.

Choose Create Record Set.

Specify the following values:

Name
Enter the domain name that you want to use to route traffic to your S3 bucket. The default value is the name of the hosted zone.

For example, if the name of the hosted zone is example.com and you want to use acme.example.com to route traffic to your bucket, enter acme.

Type
Choose A – IPv4 address.

Alias
Choose Yes.

Alias Target
In the S3 website endpoints section of the list, choose the bucket that has the same name that you specified for Name.

Routing Policy
Choose the applicable routing policy. For more information, see Choosing a Routing Policy.

Evaluate Target Health
Accept the default value of No.

Choose Create.


###5. Validate That It Worked
Your static website has been uploaded to your S3 website bucket. You can go to www.my-awesome-site.com and your static website loads from your S3 bucket.

