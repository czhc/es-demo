# es-demo

# Creating AES Domain

1. Choose Deployment type: 
    1. Deployment type: `Development and testing`
    2. Version: `7.1`
    
2. Configure domain
    1. Elasticsearch domain name: `movies`
    2. Instance type: `t2.small.elasticsearch`
    3. Number of nodes: `3`
    4. Data nodes storage type: `EBS`
    5. EBS Volume type: `General Purpose`
    6. EBS storage size per node: `10` GiB
    7. Dedicated master nodes: <skip>
    8. Optional ES cluster settings > Allow APIs that can span multiple indices and bypass index-specific access policies > :ballot_box_with_check:
  
3. Configure access and security 

    1. Public access
    2. Fine-grained access control: skip
    3. Enable Cognito Authentication: 
        1. Create Cognito User Pool. 
        2. In User Pool, create Domain name and Add an app client.
        3. Enable username password based authentication.
        2. Create Cognito Identity Pool
    4. JSON defined access policy: 
    
        ```
        {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "AWS": "*"
              },
              "Action": "es:*",
              "Resource": "arn:aws:es:us-east-1:<ACCOUNT_ID>:domain/<DOMAIN_ID>/*",
              "Condition": {
                "IpAddress": {
                  "aws:SourceIp": "<YOUR_IP>/24"
                }
              }
            }
          ]
        }
        ```
    5. Require HTTPS for all traffic, enable node-to-node encryption. (t2.small does not support encryption at rest)
    
