# Moving to AWS Lambda

### pictures to source folder copy from sam project !!!!!!!!!!!!!!!!!

cd ~/environment/react-amplified/

npm install


npm install -g @aws-amplify/cli

cd /home/ec2-user/environment/react-amplified

boundary_policy=$(aws iam list-policies --query 'Policies[?PolicyName==`AmplifyPermissionsBoundary`].Arn' --output text)

amplify init --permissions-boundary $boundary_policy


amplify add hosting

amplify publish --yes

amplify add auth

amplify push --yes

cat ~/environment/react-amplified/amplify/backend/amplify-meta.json

APP_CLIENT_ID_WEB=$(jq -r '.auth | .[keys[0]] | .output.AppClientIDWeb' ~/environment/react-amplified/amplify/backend/amplify-meta.json)

USER_POOL_ID=$(jq -r '.auth | .[keys[0]] | .output.UserPoolId' ~/environment/react-amplified/amplify/backend/amplify-meta.json)

printf "\n\nUserPoolId placeholder value: $USER_POOL_ID\n\nAppClientIDWeb placeholder value: $APP_CLIENT_ID_WEB\n\n"


clear
before=$(cat ~/environment/api-backend-sam/template.yaml | head -41 | tail -7)
update1=$(sed -i "s/REPLACE_WITH_USERPOOL_ID/$USER_POOL_ID/g" ~/environment/api-backend-sam/template.yaml)
update2=$(sed -i "s/REPLACE_WITH_APP_CLIENT_ID_WEB/$APP_CLIENT_ID_WEB/g" ~/environment/api-backend-sam/template.yaml)
after=$(cat ~/environment/api-backend-sam/template.yaml | head -41 | tail -7)

printf "template.yaml --> code BEFORE update:\n\n$before; \n\ntemplate.yaml --> code AFTER update:\n\n $after\n\n"


cd ~/environment/api-backend-sam
sam build


API_URL=$(aws cloudformation describe-stacks --stack-name sam-lab --query 'Stacks[0].Outputs[?OutputKey==`Api`].OutputValue' --output text | sed 's/\/$//'); echo ""; echo "URL = $API_URL"; echo ""

clear
before1=$(cat ~/environment/react-amplified/src/App.js | head -15 | tail -2)
update3=$(sed -i "s|REPLACE_ME_WITH_THE_API_URL|$API_URL|g" /home/ec2-user/environment/react-amplified/src/App.js)
after1=$(cat ~/environment/react-amplified/src/App.js | head -15 | tail -2)
printf "App.js --> code BEFORE update:\n\n$before1\n\nApp.js --> code AFTER update:\n\n$after1\n\n"



cd ~/environment/react-amplified

amplify publish --yes



### Hints


# Set variables
baseUrl=$API_URL
uniqueGridId=`date +%s`
curlOutput=$(curl -s -X POST "${baseUrl}/generate_grid?uniqueGridId=${uniqueGridId}")

# Print variable values
echo -e "\nbaseUrl: ${API_URL}\n"; echo -e "uniqueGridId: ${uniqueGridId}\n" ;  echo -e "Curl command output: ${curlOutput}\n"
