# genesys-copilot-summary

This is an example of how to receive the the Agent Copilot interaction summary and other details once generated. You can then trigger a `workFlow` with this information to save it where required eg a CRM. This is delivered here as an example only of the art of the possible.

## Step 1

This example uses "Implicit Grant" OAuth2 to your Genesys Cloud ORG environment. The first thing you will need to do is to get a ClientID from your Genesys Cloud ORG with the ability to connect to the API endpoints. This will also need to have a redirect to the Hosting location of the server url for example: "http://localhost:8080/index.html". You will also need add the required "scopes" for the endpoints that you want to use. In this example we are using "conversations".

![](/docs/images/oauth.png?raw=true)

Save this and copy the

    "clientId"

as you will need this later for the authentication.

## Step 2

Now create the [Architect Workflow](https://help.mypurecloud.com/articles/create-call-flow/) and create the data inputs as below.

```
Flow.conversationId
Flow.reason
Flow.resolution
Flow.summary
```

NOTE the `Flow.` part will auto append to the name as long as you create it from the Resources -> Data section. These are all of `type` string.

![](/docs/images/workflow.png?raw=true)

Now make sure that you "Publish" the flow as well as copy the "flowId" out of the URL so you can use this later on. It will look like this:

![](/docs/images/flow.png?raw=true)

## Step 3

Create the [Agent Script](https://help.mypurecloud.com/articles/create-script/) This can be editing an existing one you already have or creating a new one. To add the code to your script you will need to put in a "Web Page" object with the below URL. The size of it can be only a few pixels as you don't need to see it it just needs to load the JavaScript and run in the background.

This is dependent on where you host the webpage of course as well as your own clientId and flowId.

    http://localhost:8080/index.html?gc_region=mypurecloud.com.au&gc_clientId=YOUR_CLIENT_ID&gc_redirectUrl=http://localhost:8080/index.html&gc_workflowId=YOUR_FLOW_ID

NOTE: I'm in the apse2 region so I have the "mypurecloud.com.au" this may differ in your region. Make sure the you update the other variables with your data from above eg:

    YOUR_CLIENT_ID & YOUR_FLOW_ID

Once you host the html file on a web server this would also be moved form localhost:8080 when this is done remember to also add it as a redirect in your OAuth client you setup earlier.

### Caution Advanced Option:

So you may of noticed that the iFrame or WebPage that you have embedded into the Agent Script has a vertical scroll bar no matter what size you make it... Personally I find this ugly and annoying. To fix this you need to Export the agent script and edit the raw JSON inside the file.

You are looking for the "height" object just before the URL you are using. Ensure that your looking at the right object and change the height sizeType to be `auto` like below.

```
"height":{"typeName":"sizing","value":{"sizeType":"auto"}}
```

then save the file and do a `replace` on th existing script with the edited file. You should now notice that the element now when you click on it is selected as "Auto Sizing"

![](/docs/images/height.png?raw=true)

This will now remove the vertical scroll bar and make the UI look that bit nicer if you have the iFrame larger then a few pixels.

## Testing

You can then send the details somewhere, in my case I simply send them into a Genesys Cloud Chat group to monitor the data. You may want to send it to a object in a CRM in the real world though.

![](/docs/images/chat.png?raw=true)
