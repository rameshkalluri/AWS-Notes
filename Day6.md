# Amazon S3

- [Amazon S3](#amazon-s3)
  - [S3 Use cases](#s3-use-cases)
  - [Amazon S3 Overview - Buckets](#amazon-s3-overview---buckets)
  - [Amazon S3 Overview - Objects](#amazon-s3-overview---objects)
  - [S3 Security](#s3-security)
  - [S3 Bucket Policies](#s3-bucket-policies)
  - [Bucket settings for Block Public Access](#bucket-settings-for-block-public-access)
  - [S3 Websites](#s3-websites)
  - [S3 - Versioning](#s3---versioning)
  - [S3 Access Logs](#s3-access-logs)
  - [S3 Replication (CRR & SRR)](#s3-replication-crr--srr)
  - [S3 Storage Classes](#s3-storage-classes)
    - [S3 Durability and Availability](#s3-durability-and-availability)
    - [S3 Standard General Purpose](#s3-standard-general-purpose)
    - [S3 Storage Classes - Infrequent Access](#s3-storage-classes---infrequent-access)
      - [S3 Standard Infrequent Access (S3 Standard-IA)](#s3-standard-infrequent-access-s3-standard-ia)
      - [S3 One Zone Infrequent Access (S3 One Zone-IA)](#s3-one-zone-infrequent-access-s3-one-zone-ia)
    - [Amazon S3 Glacier Storage Classes](#amazon-s3-glacier-storage-classes)
      - [Amazon S3 Glacier Instant Retrieval](#amazon-s3-glacier-instant-retrieval)
      - [Amazon S3 Glacier Flexible Retrieval (formerly Amazon S3 Glacier)](#amazon-s3-glacier-flexible-retrieval-formerly-amazon-s3-glacier)
      - [Amazon S3 Glacier Deep Archive - for long term storage](#amazon-s3-glacier-deep-archive---for-long-term-storage)
    - [S3 Intelligent-Tiering](#s3-intelligent-tiering)
  - [S3 Object Lock & Glacier Vault Lock](#s3-object-lock--glacier-vault-lock)
  - [Shared Responsibility Model for S3](#shared-responsibility-model-for-s3)


## S3 Use cases

- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud storage
- Application hosting
- Media hosting
- Data lakes & big data analytics
- Software delivery
- Static website

## Amazon S3 Overview - Buckets

- Amazon S3 allows people to store objects (files) in “buckets” (directories)
- Buckets must have a globally unique name (across all regions all accounts)
- Buckets are defined at the region level
- S3 looks like a global service but buckets are created in a region
- Naming convention
  - No uppercase
  - No underscore
  - 3-63 characters long
  - Not an IP
  - Must start with lowercase letter or number

## Amazon S3 Overview - Objects

- Objects (files) have a Key
- The key is the FULL path:
  - s3://my-bucket/my_file.txt
  - s3://my-bucket/my_folder1/another_folder/my_file.txt
- The key is composed of **prefix** + **object name**
  - s3://my-bucket/my_folder1/another_folder/my_file.txt
- There’s no concept of “directories” within buckets (although the UI will trick you to think otherwise)
- Just keys with very long names that contain slashes (“/”)
- Object values are the content of the body:
  - Max Object Size is 5TB (5000GB)
  - If uploading more than 5GB, must use “multi-part upload”
- Metadata (list of text key / value pairs – system or user metadata)
  - Tags (Unicode key / value pair – up to 10) – useful for security / lifecycle
  - Version ID (if versioning is enabled)

## S3 Security

- **User based**
  - IAM policies - which API calls should be allowed for a specific user from IAM console
- **Resource Based**
  - Bucket Policies - bucket wide rules from the S3 console - allows cross account
  - Object Access Control List (ACL) – finer grain
  - Bucket Access Control List (ACL) – less common
- **Note:** an IAM principal can access an S3 object if
  - the user IAM permissions allow it OR the resource policy ALLOWS it
  - AND there’s no explicit DENY
- **Encryption:** encrypt objects in Amazon S3 using encryption keys

## S3 Bucket Policies

- JSON based policies
  - Resources: buckets and objects
  - Actions: Set of API to Allow or Deny
  - Effect: Allow / Deny
   Principal: The account or user to apply the policy to
- Use S3 bucket for policy to:
  - Grant public access to the bucket
  - Force objects to be encrypted at upload
  - Grant access to another account (Cross Account)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3::examplebucket/*"
      ]
    }
  ]
}
```

## Bucket settings for Block Public Access

- Block all public access: On
  - Block public access to buckets and objects granted through new access control lists (ACLS): On
  - Block public access to buckets and objects granted through any access control lists (ACLS): On
  - Block public access to buckets and objects granted through new public bucket or access point policies: On
  - Block public and cross-account access to buckets and objects through any public bucket or access point policies: On

- These settings were created to prevent company data leaks
- If you know your bucket should never be public, leave these on
- Can be set at the account level

## S3 Websites

- S3 can host static websites and have them accessible on the www
- The website URL will be:
- bucket-name.s3-website-AWS-region.amazonaws.com
  OR
- bucket-name.s3-website.AWS-region.amazonaws.com
- **If you get a 403 (Forbidden) error, make sure the bucket policy allows public reads!**

## S3 - Versioning

- You can version your files in Amazon S3
- It is enabled at the bucket level
- Same key overwrite will increment the “version”: 1, 2, 3….
- It is best practice to version your buckets
  - Protect against unintended deletes (ability to restore a version)
  - Easy roll back to previous version
- Notes:
  - Any file that is not versioned prior to enabling versioning will have version “null”
  - Suspending versioning does not delete the previous versions

## S3 Access Logs

- For audit purpose, you may want to log all access to S3 buckets
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- That data can be analyzed using data analysis tools…
- Very helpful to come down to the root cause of an issue, or audit usage, view suspicious patterns, etc…

## S3 Replication (CRR & SRR)

- Must enable versioning in source and destination
- Cross Region Replication (CRR)
- Same Region Replication (SRR)
- Buckets can be in different accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- CRR - Use cases: compliance, lower latency access, replication across accounts
- SRR – Use cases: log aggregation, live replication between production and test accounts

## S3 Storage Classes

- [Amazon S3 Standard - General Purpose](#s3-standard-general-purpose)
- [Amazon S3 Standard - Infrequent Access (IA)](#s3-standard-infrequent-access-s3-standard-ia)
- [Amazon S3 One Zone - Infrequent Access](#s3-one-zone-infrequent-access-s3-one-zone-ia)
- [Amazon S3 Glacier Instant Retrieval](#amazon-s3-glacier-instant-retrieval)
- [Amazon S3 Glacier Flexible Retrieval](#amazon-s3-glacier-flexible-retrieval-formerly-amazon-s3-glacier)
- [Amazon S3 Glacier Deep Archive](#amazon-s3-glacier-deep-archive-–-for-long-term-storage)
- [Amazon S3 Intelligent Tiering](#s3-intelligent-tiering)

- Can move between classes manually or using S3 Lifecycle configurations

### S3 Durability and Availability

- Durability:
  - High durability (99.999999999%, 11 9’s) of objects across multiple AZ
  - If you store 10,000,000 objects with Amazon S3, you can on average expect to incur a loss of a single object once every 10,000 years
  - Same for all storage classes
- Availability:
  - Measures how readily available a service is
  - Varies depending on storage class
  - Example: S3 standard has 99.99% availability = not available 53 minutes a year

### S3 Standard General Purpose

- 99.99% Availability
- Used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures
- Use Cases: Big Data analytics, mobile & gaming applications, content distribution…

### S3 Storage Classes - Infrequent Access

- For data that is less frequently accessed, but requires rapid access when needed
- Lower cost than S3 Standard

#### S3 Standard Infrequent Access (S3 Standard-IA)

- 99.9% Availability
- Use cases: Disaster Recovery, backups

#### S3 One Zone Infrequent Access (S3 One Zone-IA)

- High durability (99.999999999%) in a single AZ; data lost when AZ is destroyed
- 99.5% Availability
- Use Cases: Storing secondary backup copies of on-premise data, or data you can recreate

### Amazon S3 Glacier Storage Classes

- Low-cost object storage meant for archiving / backup
- Pricing: price for storage + object retrieval cost

#### Amazon S3 Glacier Instant Retrieval

- Millisecond retrieval, great for data accessed once a quarter
- Minimum storage duration of 90 days

#### Amazon S3 Glacier Flexible Retrieval (formerly Amazon S3 Glacier)

- Expedited (1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) – free
- Minimum storage duration of 90 days

#### Amazon S3 Glacier Deep Archive - for long term storage

- Standard (12 hours), Bulk (48 hours)
- Minimum storage duration of 180 days

### S3 Intelligent-Tiering

- Small monthly monitoring and auto-tiering fee
- Moves objects automatically between Access Tiers based on usage
- There are no retrieval charges in S3 Intelligent-Tiering
- Frequent Access tier (automatic): default tier
- Infrequent Access tier (automatic): objects not accessed for 30 days
- Archive Instant Access tier (automatic): objects not accessed for 90 days
- Archive Access tier (optional): configurable from 90 days to 700+ days
- Deep Archive Access tier (optional): config. from 180 days to 700+ days

## S3 Object Lock & Glacier Vault Lock

- S3 Object Lock
  - Adopt a WORM (Write Once Read Many) model
  - Block an object version deletion for a specified amount of time
- Glacier Vault Lock
  - Adopt a WORM (Write Once Read Many) model
  - Lock the policy for future edits (can no longer be changed)
  - Helpful for compliance and data retention

## Shared Responsibility Model for S3

| AWS                                                                                                           | YOU                                                     |
| ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| Infrastructure (global security, durability, availability, sustain concurrent loss of data in two facilities) | S3 Versioning, S3 Bucket Policies, S3 Replication Setup |
| Configuration and vulnerability analysis                                                                      | Logging and Monitoring, S3 Storage Classes              |
| Compliance validation                                                                                         | Data encryption at rest and in transit                  |

## AWS Snow Family

- Highly-secure, portable devices to collect and process data at the edge, and migrate data into and out of AWS
- Data migration:
  - Snowcone
  - Snowball Edge
  - Snowmobile
- Edge computing:
  - Snowcone
  - Snowball Edge

