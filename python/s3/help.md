# Moving to AWS Lambda

### pictures to source folder copy from sam project !!!!!!!!!!!!!!!!!

cd local-script

pip install -r requirements.txt


python main.py


## S3

cd ~/environment/s3-script
pip install -r requirements.txt




source_bucket=$(aws s3api list-buckets --output text --query 'Buckets[?contains(Name, `source-images`) == `true`] | [0].Name')

destination_bucket=$(aws s3api list-buckets --output text --query 'Buckets[?contains(Name, `destination-images`) == `true`] | [0].Name') ;

printf "\nSource bucket: $source_bucket\nDestination bucket: $destination_bucket\n\n"


python main.py $source_bucket $destination_bucket

