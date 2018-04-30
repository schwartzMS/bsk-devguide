
[block:callout]
{
  "type": "danger",
  "title": "The Box Skills Kit is in a Private Beta",
  "body": "The Box Skills Kit is currently available in a closed private beta and is subject to usability and performance issues as well as breaking changes without notice. During this period, the Box Skills Kit is not recommended for production use."
}
[/block]
A Box Skills is a function that performs custom processing on a file stored in Box. In the current framework, a Box Skill downloads a file from Box, passes it to a service for processing (e.g. an image recognition service for labeling objects in an image file) and writes the results of the processing to the file object in Box. For more information, please see [the Box Skills overview](doc:getting-started-with-box-skills).

To create a Box Skill, please follow the steps below:
[block:api-header]
{
  "type": "basic",
  "title": "1. Sign Up for a Box Developer Account"
}
[/block]
[Sign up for a Box Developer account](http://account.box.com/signup/n/developer). Here, you will receive your own instance of Box for which you are the admin. You will need to provide your enterprise ID and any specific folder IDs for which you want your skill to be enabled later on.

[block:api-header]
{
  "title": "2. Create a Box Skill function"
}
[/block]
*The steps below provide instructions for setting up a function using AWS Lambda. You are welcome to use any serverless platform for your Box Skill, so long as you can retrieve an invoke URL where the event payload from Box can be sent. Depending on your platform, the instructions below may be incorrect.*

  * Sign up for an [Amazon developer account](https://portal.aws.amazon.com/billing/signup#/start)
  * Navigate to the Lambda Management Console 
  * Click "Create Function"
  * Select "Author from scratch"
  * Give your function a name and set the runtime to "Node.js 6.10"
  * Select "Create a custom role." This will open a new tab to configure your custom role. Use the default settings and click "Allow"
  * Now, select the "lambda_basic_execution" under the "Existing role" dropdown
  * Click "Create function"
  * Under the "Designer" section, click the "API Gateway" option as the trigger for your function.
  * Under the "Configure triggers" section, select "Create a New API" and enter an API name. This should be the same name as your function
  * Set the "Deployment stage" set to prod and the "Security" to "Open." Click "Add"
  * Click "Save" in the upper-right hand corner
  * Under the "Designer" section, click on your function's name
  * Under the "Function code" section, change the drop-down under "Code entry type" to "Upload a zip file"
  * Upload your Lambda code.  Here is a [sample zip file](https://cloud.box.com/s/r9zixlaszbgwjeywxqqtcjdtwv8qrbd1) that you can use that leverages the [Microsoft Azure Video Indexer API](https://azure.microsoft.com/en-us/services/cognitive-services/video-indexer/) to perform an analysis of a video file from Box.
  * Under the "Environment Variables" section, you will need to add keys and values depending on the ML provider that you are using. In this case, we'll walk through the sample zip file and what keys are needed. This section can vary significantly with each different ML provider or even the code that you're executing. The sample zip file utilizes the following keys to execute the skill through Microsoft Azure. You can register for your own Microsoft Azure Video Indexer API account [here](https://videobreakdown.portal.azure-api.net/products).
[block:parameters]
{
  "data": {
    "h-0": "Key",
    "h-1": "Value",
    "0-0": "BOX_API_ENDPOINT",
    "1-0": "BOX_CLIENT_ID",
    "2-0": "BOX_SKILLS_MS_VIDEO_INDEXER_CALLBACK_ENDPOINT",
    "3-0": "MICROSOFT_VIDEO_INDEXER_API_KEY",
    "4-0": "MS_VIDEO_BREAKDOWN_API_ENDPOINT",
    "5-0": "SYMMETRIC_ENCRYPTION_SECRET",
    "6-0": "TRANSCRIPT_LANGUAGE",
    "0-1": "https://api.box.com/2.0",
    "1-1": "Your Box Client ID from the Box Developer Console. You will receive this after completing Step 3 below.",
    "2-1": "Your AWS API Gateway URL from the steps above",
    "3-1": "Primary Key from Microsoft Video Indexer service",
    "4-1": "https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns",
    "5-1": "Any string you want to use for encryption",
    "6-1": "en-US"
  },
  "cols": 2,
  "rows": 7
}
[/block]
  * Under the "Basic settings" section, set the timeout to 30 seconds
  * Click "Save"upper-right-hand corner. Your AWS Lambda function is ready to go! 
  * Under the "Designer" section, click on API Gateway. Under the "API Gateway" section, expand the "Details" drop-down and note the "Invoke URL" field. This is the URL that you will direct your event pump payload to 
[block:api-header]
{
  "title": "3. Create a Box Skill application"
}
[/block]
  * [Complete the form here](https://docs.google.com/forms/d/e/1FAIpQLSc21S58Z8qAGW9nHxmSOfDpiRSmhxIvCwylnmd9eDtdP5Rpxw/viewform?usp=sf_link). In order to submit your skill, you need to know the EID and the id(s) for the folders for which you want the skill enabled if you don't want it enabled on an entire enterprise. You can find your EID on your "Account Info" tab in "Business Settings" using the Box Admin Console. To access Business Settings, click on the gear in the top right corner of the Admin Console, then click Business Settings.  
  * Eventually, all of this information will be submitted via the Box Developer Console. A member of the skills team will enable your skill for you and provide you with the details you need to complete the configuration in Step 2. 
[block:api-header]
{
  "title": "4: Test your Box Skill"
}
[/block]
  * Upload a *short* video to the folder that is enabled for your skill. You can [download this video](https://platformdemo.box.com/s/m3hx5n3v2ygyrpp8dgisw1h7i4qbzej4) for demo purposes.
  * After about 10 seconds, you should see the keywords applied by the machine learning provider in the "Details" section of the sidebar of the Box web application preview
  * If you do not see keywords, check your AWS CloudWatch logs for errors. There may be an issue with your AWS Lambda function 
[block:api-header]
{
  "title": "5. Customize your Box Skill"
}
[/block]
This tutorial guided you through creating a skill that processes video files using Microsoft Video Indexer API. You can try using a different machine learning API or customize the output from the Video Indexer API to fit a new use case.