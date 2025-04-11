# AWS_sequence_analysis

### Prerequisites: 
1) set up the s3 - lambda - s3 pipline we set up in class via the video on slack. Be sure the the lambda has AmazonS3FullAccess and AWSLambdaBasicExecutionRole
2) set up the lambda - RDS the way we did in class. In that RDS be sure that there is a database named lambda_demo_db. if you run the code form class once, you will have created what you need to
  2a) be sure that you connected the RDS to the lambda correctly

SETUP
The new archetecture is S3-lambda-lambda-RDS
this is because, one lambda must be on the VPC, the other *cannot* be on the vpc. 
- This also increases the atomocity of a lambda's function.
- <img width="904" alt="image" src="https://github.com/user-attachments/assets/258a5186-3942-4e9d-b169-3d5b1306b4fc" />


### Lambda 1 setup:
there is a SAM in the rept names lambda1. be careful if using this, as the enpoints (s3 bucket and other lambda are hard coded into it)
###### If you want to do this from scratch
1) Create a ROLE in IAM with AmazonS3FullAccess and AWSLambdaBasicExecutionRole.
2) connect it to the buckets like we did in class. (there are two, the input and the output) make these *two diffrent buckets*
   2a) either leave the suffix *blank* or add *.fasta* the default we did in class only takes csvs
3) in permissions: click on the role in "Execution role." Add Permissions > "Add Inline Policy" > JSON
4) Copy paste this:
```python
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "AllowInvokeLambda2",
			"Effect": "Allow",
			"Action": [
				"lambda:InvokeFunction"
			],
			"Resource": [
				"arn:aws:lambda:us-east-2:376129863520:function:ds4300-rds-inclass"
			]
		}
	]
}
```
6) change the name of the resource to the endpoint of lambda2. Mine is called ds4300-rds-inclass.
7) copy paste the code in this repo under lambda1 into the code section. DO NOT FORGET TO CHANGE THE GLOBAL VARS

### Lambda 2 setup:
there is a SAM in the rept names lambda2. be careful if using this, as the enpoints (s3 bucket and other lambda are hard coded into it)
###### If you want to do this from scratch
1) If you have the lambda-rds created from class. all you need to do is:
2) ensure that you have
```python
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "rds:DescribeDBInstances",
            "Resource": "*"
        }
    ]
}
```
you should have this from connecting the RDS database
4) copy paste the code form lambda2 into the code section BE SURE TO SET THE GLOBAL VARIABLES
I included the test file that I used to ensure that this all works.

```python
Here is the format that the data is in the RDS
id	sample_name	adenine_pct	guanine_pct	cytosine_pct	thymine_pct	gc_content	melting_temp	length	molecular_weight
1	sampled sequence 1 consisting of 100 bases.	77.0	0.0	0.0	23.0	0.0	200	100	33000
2	sampled sequence 2 consisting of 200 bases.	60.0	20.0	10.0	10.0	30.0	220	200	66000
3	sampled sequence 3 consisting of 150 bases.	50.0	30.0	10.0	10.0	40.0	230	150	49500
4	sampled sequence 4 consisting of 120 bases.	65.0	15.0	10.0	10.0	25.0	210	120	39600
5	sampled sequence 5 consisting of 180 bases.	70.0	10.0	10.0	10.0	20.0	250	180	59400
```














