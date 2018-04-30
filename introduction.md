
[block:callout]
{
  "type": "danger",
  "title": "The Box Skills Kit is in a Private Beta",
  "body": "The Box Skills Kit is currently available in a closed private beta and is subject to usability and performance issues as well as breaking changes without notice. During this period, the Box Skills Kit is not recommended for production use."
}
[/block]

[block:api-header]
{
  "title": "Introduction"
}
[/block]
Box Skills is a framework that makes it easy to integrate machine learning services with Box to enhance files in Box and add rich metadata to unstructured data. Box Skills apply technologies like image recognition, speech-to-text transcription, natural language understanding and more to add structure or extract information from documents, images, videos, audio files and other files that live in Box.

A Box Skill is a function retrieves a file from Box, processes it using a third-party machine learning service and writes the output of the processing as metadata on the file object in Box.

The Box Skills Kit is a developer toolkit that makes it easy to learn about the Box Skills framework and build custom skills. The Box Skills framework leverages existing Box APIs and SDKs.

The Box Skills Kit includes:
  * Documentation and guides for building custom skills
  * Sample skills built by Box and other developers building skills
[block:api-header]
{
  "title": "Building a Box Skill"
}
[/block]
In order for a custom skill to be run on a file in Box, the following needs to happen: 
  * A developer writes the skill function and deploys to a serverless development platform (e.g. AWS Lambda)
  * A developer completes the form found [here](https://docs.google.com/forms/d/e/1FAIpQLSc21S58Z8qAGW9nHxmSOfDpiRSmhxIvCwylnmd9eDtdP5Rpxw/viewform) with information about the skill function. In a future state, Box Skills will be created and configured in the Box Developer Console
  * The skill is configured on behalf of the developer by a Box employee via an internal tool using the information submitted by the developer. In a future state, a Box Admin would authorize a skill in the Box Admin Console
  * The skill function is triggered when a file is uploaded to one of the specified folders

When a file is uploaded to a folder with a skill configured on it, an event will be sent from Box to an invoke URL provided by the developer to Box. The event payload includes information about the file to be processed and access tokens to download that file and write the results of the processing as metadata on that same file object. 

Here is a sample event payload: 
[block:code]
{
  "codes": [
    {
      "code": "{  \n   \"type\":\"skill_invocation\",\n   \"skill\":{  \n      \"type\":\"app\",\n      \"id\":\"32066\",\n      \"name\":\"Manish Test Skill\",\n      \"client_id\":\"dgsn72unoymjhy8xqluherhimo2tw1hk\"\n   },\n   \"token\":{  \n      \"write\":{  \n         \"access_token\":\"REDACTED\",\n         \"expires_in\":1519256994,\n         \"restricted_to\":\"[{\\\"scope\\\":\\\"gcm\\\"},{\\\"scope\\\":\\\"item_upload\\\",\\\"object_id\\\":6399767106,\\\"object_type\\\":\\\"file\\\"}]\",\n         \"token_type\":\"bearer\"\n      },\n      \"read\":{  \n         \"access_token\":\"REDACTED\",\n         \"expires_in\":1519256994,\n         \"restricted_to\":\"[{\\\"scope\\\":\\\"gcm\\\"},{\\\"scope\\\":\\\"item_read\\\",\\\"object_id\\\":6399767106,\\\"object_type\\\":\\\"file\\\"}]\",\n         \"token_type\":\"bearer\"\n      }\n   },\n   \"id\":\"11111111-2222-442f-9961-d056f0b68594\",\n   \"created_at\":\"2017-12-27T19:19:34-08:00\",\n   \"trigger\":\"FILE_CONTENT\",\n   \"enterprise\":{  \n      \"type\":\"enterprise\",\n      \"id\":\"1635603\",\n      \"name\":\"Enterprise for Skill Tester\"\n   },\n   \"source\":{  \n      \"type\":\"file\",\n      \"id\":\"6399767106\",\n      \"sequence_id\":\"1\",\n      \"name\":\"test_manish.png\",\n      \"file_version\":{  \n         \"id\":\"25583725616\"\n      },\n      \"parent\":{  \n         \"type\":\"folder\",\n         \"id\":\"6471024962\",\n         \"sequence_id\":null,\n         \"etag\":null,\n         \"name\":\"Manish Test\"\n      },\n      \"owner_enterprise_id\":\"1635603\"\n   },\n   \"event\":{  \n      \"event_id\":\"11111111-2222-442f-9961-d056f0b68594\",\n      \"event_type\":\"ITEM_UPLOAD\",\n      \"created_at\":\"2017-12-27T19:19:34-08:00\",\n      \"created_by\":{  \n         \"type\":\"user\",\n         \"id\":\"4295234688\"\n      },\n      \"source\":{  \n         \"type\":\"file\",\n         \"id\":\"6399767106\",\n         \"sequence_id\":\"1\",\n         \"name\":\"test_manish.png\",\n         \"file_version\":{  \n            \"id\":\"25583725616\"\n         },\n         \"parent\":{  \n            \"type\":\"folder\",\n            \"id\":\"6471024962\",\n            \"sequence_id\":null,\n            \"etag\":null,\n            \"name\":\"Manish Test\"\n         },\n         \"owner_enterprise_id\":\"1635603\"\n      }\n   },\n   \"parameters\":{  \n\n   }\n}",
      "language": "json"
    }
  ]
}
[/block]
With this event, the skill function then sends information about the file to another service for processing. The service downloads and analyzes the file using whatever analysis a developer has requested. Once the processing is finished, the output of a skill is then written as metadata on the file object in Box. That metadata is displayed in the Box user interface using a series of pre-designed cards. This information is also indexed in Box's search, can trigger other Box products (such as workflows in Box Relay or [retention policies](ref:retention-policy-object) with Box Governance) and is accessible via the API.

Currently, skills can only be triggered based on an upload event and can only write metadata to the file object in Box. 
[block:api-header]
{
  "title": "Metadata-driven \"Cards\""
}
[/block]
The output of a skill is displayed in the Box web application in the sidebar when a user is previewing a file using a series of pre-designed "cards".  These cards are designed to handle the most common types of output from machine learning algorithms and are based on a pre-built [Metadata Template](ref:metadata-templates).

Currently, there are three card types available:
  * **Keyword Card** - A list of keywords associated with the file as a whole
  * **Timeline Card** - A list of images or words that are associated with particular time ranges of a media file
  * **Transcript Card** - The transcribed text of a media file with associated time references

Each card has an optional title.  The example below shows a timeline card with the title "Faces".  If the title is omitted, then the title will default to "Keywords", "Timeline", or "Transcript".

Timeline cards can also display either images (e.g. faces) or labels (e.g. object labels or sentiments).

**Keyword card:** 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/bda5ba5-keywords.png",
        "keywords.png",
        313,
        212,
        "#f6f6f9"
      ],
      "caption": ""
    }
  ]
}
[/block]
**Timeline card:**
With faces:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/d2d3b95-timelinefaces.png",
        "timelinefaces.png",
        304,
        165,
        "#f4f3f5"
      ]
    }
  ]
}
[/block]
With topics:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/08f0c7d-timeline_topics.png",
        "timeline topics.png",
        311,
        245,
        "#f9f9f8"
      ]
    }
  ]
}
[/block]
**Transcript card:**
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/48858e9-transcript.png",
        "transcript.png",
        314,
        359,
        "#f80000"
      ]
    }
  ]
}
[/block]
