# Week 0 — Billing and Architecture
Installing AWS CLI in gitpod environment completed    
Creating a New IAM user and Generating credentials completed    

Env vars set for gitpod environment     

AWS CLI working as expected      

{
    "UserId": "AIDAV4LU****_5RZ323CNU",
    "Account": "40450****_813",
    "Arn": "arn:aws:iam::40450****813:user/ManojKumar"
}      
     
Enable Billing alerts completed       

Create SNS topic completed       

aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint manoj@gmail.com       
    
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:40450****813:billing-alarm \
    --protocol email \
    --notification-endpoint manoj@gmail.com        
    
Create Alarm completed        

aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json     

Create a AWS budget completed       

aws budgets create-budget \
    --account-id 40450****813 \
    --budget file://budget.json \
    --notifications-with-subscribers file://budget-notifications-with-subscriber.json      
    
    
# Cruddder Arch

Link to Lucid Chart: https://lucid.app/lucidchart/626539a1-d299-4782-b3c5-efc2996d3cb3/edit?viewport_loc=-3135%2C-31%2C4245%2C1545%2C0_0&invitationId=inv_d8a9955c-639d-417d-8632-423ceec9a779       

![image](https://user-images.githubusercontent.com/88241057/218769202-d45c9190-28d2-4d8d-a7ae-68f20ca927d1.png)

