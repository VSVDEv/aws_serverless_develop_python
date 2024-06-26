# Moving to AWS Lambda

### pictures to source folder copy from sam project

cd ~/environment/lambda-function ; pip install -r requirements.txt --target ./package

cd ~/environment/lambda-function/package ; zip -r ~/environment/lambda-function.zip .

cd ~/environment/lambda-function ; zip ~/environment/lambda-function.zip lambda_function.py

aws lambda create-function \
--function-name grid-maker \
--runtime python3.9 \
--timeout 30 \
--handler lambda_function.lambda_handler \
--role $LAMBDA_ROLE \
--zip-file fileb://~/environment/lambda-function.zip



destination_bucket=$(aws s3api list-buckets --output text --query 'Buckets[?contains(Name, `destination-images`) == `true`] | [0].Name')

source_bucket=$(aws s3api list-buckets --output text --query 'Buckets[?contains(Name, `source-images`) == `true`] | [0].Name')

printf "{\n    \"SOURCE_BUCKET\": \"$source_bucket\",\n    \"DESTINATION_BUCKET\": \"$destination_bucket\"\n}" > ~/environment/lambda-function/event.json

printf "\nThe event.json file has been updated with the following content:\n\n" ; cat event.json ; printf "\n\n"


aws lambda invoke --payload fileb://event.json --function-name grid-maker ~/environment/output.txt && cat ~/environment/output.txt | tr -d '"' > ~/environment/output_without_quotes.txt && mv ~/environment/output_without_quotes.txt ~/environment/output.txt && echo -e "\nS3 presigned URL to copy: \n" && cat ~/environment/output.txt && echo -e "\n"

aws logs tail /aws/lambda/grid-maker --follow --format json

aws lambda invoke --payload fileb://event.json \
--function-name grid-maker ~/environment/output.txt

