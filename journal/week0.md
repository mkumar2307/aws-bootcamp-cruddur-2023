# Week 0 — Billing and Architecture
Installing AWS CLI in gitpod environment completed
Creating a New IAM user and Generating credentials completed
Env vars set for gitpod environment
AWS CLI working as expected

{
    "UserId": "AIDAV4LUHWZS5RZ323CNU",
    "Account": "404507571813",
    "Arn": "arn:aws:iam::404507571813:user/ManojKumar"
}

Enable Billing alerts completed

Create SNS topic completed

aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint manojgudipati@hotmail.com

aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:404507571813:billing-alarm \
    --protocol email \
    --notification-endpoint manojgudipati@hotmail.com


Create Alarm completed

aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json

Create a AWS budget completed

aws budgets create-budget \
    --account-id 404507571813 \
    --budget file://budget.json \
    --notifications-with-subscribers file://budget-notifications-with-subscriber.json
