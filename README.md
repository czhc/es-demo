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
        2. In User Pool, create Domain name.
        2. Create Cognito Identity Pool. Check App integration for default-created App client. 
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

# Loading and Reading Data into Domain

1. From working directory, run 

    ```
        awscurl --service es -XPOST $ES/_bulk -H 'Content-Type: application/json' -d "@bulk_movies.json"
    ```

    `awscurl` is a python package that wraps around curl to authenticate via AWS Signature V4 Signing Process. Install it via `pip`.
    
    Output: 
    ```
        {"took":19053,"errors":false,"items":[{"index":{"_index":"movies","_type":"_doc","_id":"2","_version":1,"result":"created","_shards":{"total":2,"successful":2,"failed":0},"_seq_no":0,"_primary_term":1,"status":201}},{"index":
        ...
    ```
2. Query inserted data: 
    
    ```
        awscurl --service es -XGET "$ES/movies/_search?q=Thriller"
    ```
    
    Output: 
    
    ```
        "took":82,"timed_out":false,"_shards":{"total":5,"successful":5,"skipped":0,"failed":0},"hits":{"total":{"value":2,"relation":"eq"},"max_score":0.41501677,"hits":[{"_index":"movies","_type":"_doc","_id":"2","_score":0.41501677,"_source":{"director": "Frankenheimer, John", "genre": ["Drama", "Mystery", "Thriller", "Crime"], "year": 1962, "actor": ["Lansbury, Angela", "Sinatra, Frank", "Leigh, Janet", "Harvey, Laurence", "Silva, Henry", "Frees, Paul", "Gregory, James", "Bissell, Whit",...
    ```
    
