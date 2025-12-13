# Week 0 — Billing and Architecture

Gipod has been changed to ONA. Solved environment and initialization issue for AWS CLI by changing configuration from .gitpod.yml to devcontainer.json. 
Added in devcontainer.json the following:
"postCreateCommand": "cd /workspaces && curl \"https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip\" -o \"awscliv2.zip\" && unzip awscliv2.zip && sudo ./aws/install && cd /workspaces/AWS-CloudCamp",   
"containerEnv": {
		"AWS_CLI_AUTO_PROMPT": "on-partial"
	},
  
Environment variables can't be created using gp command, so instead added in project->edit->secrets->new secret and added as environment variables.

Added AWS_ACCOUNT_ID also inside devcontainer.json to be able to add the budget alarm to the account.
added 2 files, budget.json and budget-notifications-with-subscribers.json inside the new folder hierarchy AWS-CloudCamp/aws/json

run the command -> aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json


Use SNS to subscribe to an email address
first run: aws sns create-topi --name billing-alarm, it will outpout:
{
    "TopicArn": "arn:aws:sns:eu-central-1:549969920082:billing-alarm"
}

then use: aws sns subscribe \
    --topic-arn arn:aws:sns:eu-central-1:549969920082:billing-alarm \
    --protocol email \
    --notification-endpoint alex.ali49@yahoo.com

to tell to what email to subscribe, with what topic, it will output the waits for confirmation on email. after confirming it will create a billing alarm in Amazon SNS, in topics tab. This SNS topic will be use to send email triggered by an alarm.

Next create an alarm in cloudwatch.
Create new file inside aws/json, called alarm-config.json where following is added
{
    "AlarmName": "DailyEstimatedCharges",
    "AlarmDescription": "This alarm would be triggered if the daily estimated charges exceeds 50$",
    "ActionsEnabled": true,
    "AlarmActions": [
        "arn:aws:sns:eu-central-1:549969920082:billing-alarm"
    ],
    "EvaluationPeriods": 1,
    "DatapointsToAlarm": 1,
    "Threshold": 1,
    "ComparisonOperator": "GreaterThanOrEqualToThreshold",
    "TreatMissingData": "breaching",
    "Metrics": [{
        "Id": "m1",
        "MetricStat": {
            "Metric": {
                "Namespace": "AWS/Billing",
                "MetricName": "EstimatedCharges",
                "Dimensions": [{
                    "Name": "Currency",
                    "Value": "USD"
                }]
            },
            "Period": 86400,
            "Stat": "Maximum"
        },
        "ReturnData": false
    },
    {
        "Id": "e1",
        "Expression": "IF(RATE(m1)>0,RATE(m1)*86400,0)",
        "Label": "DailyEstimatedCharges",
        "ReturnData": true
    }]
}
Inside alarm actions is the sns topic created before. Next run the command aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm-config.json. Now in AWS, CloudWatch inside alarms, the alarm can be seen.

Day2.
Using IAM. I created an organization and added business units. Created an account, which I can use. For example If I am logged as an user I can switch to that account using switch Role from Account dropdown menu. There I pick the accountId I want to use. Only I as management account can switch freely between accounts.

Week0 Challenge: Created lucid chart <img width="1015" height="648" alt="image" src="https://github.com/user-attachments/assets/2039cf5e-ce6a-4685-a1e5-11878e90b755" />
https://lucid.app/lucidchart/59a5e77e-2222-4860-84b5-b22a8aedf96b/edit?view_items=fNBdqczpR_lA&page=WlAdhe_n5xVt&invitationId=inv_df3efa31-e39c-4125-9a48-072ef04a1113

