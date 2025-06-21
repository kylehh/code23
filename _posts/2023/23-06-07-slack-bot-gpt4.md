---
title: Building Slack Bot for LLM on AWS - Part 2
mathjax: true
toc: true
categories:
  - Application
tags:
  - AWS
  - GenAI
  - LLM
---

"How to create Slack bot which interact with AWS Lambda"  
This is the prompt I entered for GPT4, and there are the answers from it

1. Create a new Slack App and Bot:
- Go to https://api.slack.com/apps and sign in to your Slack account.
- Click "Create New App", provide a name and select a workspace for development, then click "Create App".
- In the "Add features and functionality" section, click on "Bots" and then "Add a bot".
- Give your bot a display name and default username, then click "Add Bot".

2. Set up API permissions:
- In the Slack App settings, go to the "OAuth & Permissions" tab.
- Scroll down to "Scopes" and add required bot token scopes (e.g., "app_mentions:read", "chat:write", "commands" and any other relevant scopes).
- Click "Install App" at the top of the page and then "Install App to Workspace".
- Authorize the app in your workspace and take note of the "Bot User OAuth Token" displayed, as you'll need it later.

3. Create an AWS Lambda function:
- Sign in to your AWS Management Console and navigate to the Lambda service.
- Click "Create function" and choose "Author from scratch".
- Provide a function name and select a runtime (e.g., Python 3.8).
- In the "Function code" section, upload your code as a .zip file or provide a repository link if using the "Code entry type" as "Upload a file from Amazon S3" or "Use a repository from GitHub or CodeCommit".
- Set up the execution role for your Lambda function.
- Click "Create function".

4. Create an API Gateway:
- In the AWS Management Console, go to the API Gateway service.
- Click "Create API" and choose "REST API".
- Select "New API" and provide a name and description.
- Create a new resource and method (e.g., POST) and set the integration type to "Lambda Function".
- Select the Lambda function you created earlier and save the changes.
- Deploy the API by selecting "Actions" > "Deploy API" and choose a deployment stage (e.g., "prod").

5. Update your Slack App with the API Gateway URL:
- Copy the "Invoke URL" from your API Gateway's "Stages" section.
- Go back to your Slack App's settings and navigate to the "Event Subscriptions" tab.
- Toggle "Enable Events" on and paste the API Gateway URL into the "Request URL" field.
- Add relevant event subscriptions (e.g., "app_mention") and save the changes.

6. Develop your Lambda function:
- Write the code for your Lambda function to handle incoming Slack events, process them, and send responses back to Slack.
- Use the "Bot User OAuth Token" from step 2 to authenticate requests to the Slack API.
- You can use libraries like slack-sdk (Python) or @slack/bolt (Node.js) to interact with the Slack API.

7. Test your Slack bot:
- Invite the bot to a channel in your Slack workspace and interact with it to test its functionality.

This is such an easy blog. Basically what GPT answers is exactly right.
I followed the steps and created the bot without any issues. Of course, my AWS experiences also helped here.

Last week I was in SF office, talked to lots of people but coded too few...