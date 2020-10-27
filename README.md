
# ATTENTION!!!

The bot is deployed into Azure using a CI/CD pipeline that is automatically triggered by commits to the master branch. For any contributions please ensure you clone the master branch and create a feature branch which you can name as ``feature/{featurename}``. Once done with your changes and tested locally, please pull any new changes that might have been added post your branching and created a new pull request.

Bot Framework v4 Proactive/Conversation Bot for Teams.

This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to send proactive messages to users by capturing a conversation reference, then using it later to initialize outbound messages. It also shows
how to incorporate basic conversational flow into a Teams application. It also illustrates a few of the Teams specific calls you can make from your bot.

# Teams Conversation Bot

Bot Framework v4 Proactive/Conversation Bot for Teams.

This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to send proactive messages to users by capturing a conversation reference, then using it later to initialize outbound messages. It also shows
how to incorporate basic conversational flow into a Teams application. It also illustrates a few of the Teams specific calls you can make from your bot.

## Prerequisites

- Microsoft Teams is installed and you have an account
- [.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1
- [ngrok](https://ngrok.com/) or equivalent tunnelling solution

## Concepts introduced

Typically, each message that a bot sends to the user directly relates to the user's prior input. In some cases, a bot may need to send the user a message that is not directly related to the current topic of conversation. These types of messages are called proactive messages.

Proactive messages can be useful in a variety of scenarios. If a bot sets a timer or reminder, it will need to notify the user when the time arrives. Or, if a bot receives a notification from an external system, it may need to communicate that information to the user immediately. For example, if the user has previously asked the bot to monitor the price of a product, the bot can alert the user if the price of the product has dropped by 20%. Or, if a bot requires some time to compile a response to the user's question, it may inform the user of the delay and allow the conversation to continue in the meantime. When the bot finishes compiling the response to the question, it will share that information with the user.

This project has a notify endpoint that will trigger the proactive messages to be sent to
all users who have previously messaged the bot.

## Local set up

> Note these instructions are for running the sample on your local machine, the tunnelling solution is required because
the Teams service needs to call into the bot.

1) Clone the repository

    ```bash
    git clone https://github.com/moseskt/alettah---help.git
    ```

- Run the bot from a terminal or from Visual Studio:

  A) From a terminal, navigate to `samples/csharp_dotnetcore/16.proactive-messages`

  ```bash
  # run the bot
  dotnet run
  ```

  B) Or from Visual Studio
   - Launch Visual Studio
   - File -> Open -> Project/Solution
   - Select `ProactiveBot.csproj` file

2) Run ngrok - point to port 3978

    ```bash
    ngrok http -host-header=rewrite 3978
    ```

3) Create [Bot Framework registration resource](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-quickstart-registration) in Azure
    - Use the current `https` URL you were given by running ngrok. Append with the path `/api/messages` used by this sample
    - Ensure that you've [enabled the Teams Channel](https://docs.microsoft.com/en-us/azure/bot-service/channel-connect-teams?view=azure-bot-service-4.0)
    - __*If you don't have an Azure account*__ you can use this [Bot Framework registration](https://docs.microsoft.com/en-us/microsoftteams/platform/bots/how-to/create-a-bot-for-teams#register-your-web-service-with-the-bot-framework)

4) Update the `appsettings.json` configuration for the bot to use the Microsoft App Id and App Password from the Bot Framework registration. (Note the App Password is referred to as the "client secret" in the azure portal and you can always create a new client secret anytime.)

5) __*This step is specific to Teams.*__
    - **Edit** the `manifest.json` contained in the  `teamsAppManifest` folder to replace your Microsoft App Id (that was created when you registered your bot earlier) *everywhere* you see the place holder string `<<YOUR-MICROSOFT-APP-ID>>` (depending on the scenario the Microsoft App Id may occur multiple times in the `manifest.json`)
    - **Zip** up the contents of the `teamsAppManifest` folder to create a `manifest.zip`
    - **Upload** the `manifest.zip` to Teams (in the Apps view click "Upload a custom app")

6) Run your bot, either from Visual Studio with `F5` or using `dotnet run` in the appropriate folder.

## Testing the bot using Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.

- Install the latest Bot Framework Emulator from [here](https://github.com/Microsoft/BotFramework-Emulator/releases)

### Connect to the bot using Bot Framework Emulator

- Launch Bot Framework Emulator
- File -> Open Bot
- Enter a Bot URL of `http://localhost:3978/api/messages`

With the Bot Framework Emulator connected to your running bot, the sample will not respond to an HTTP GET that will trigger a proactive message.  The proactive message can be triggered from the command line using `curl` or similar tooling, or can be triggered by opening a browser windows and navigating to `http://localhost:3978/api/notify`.

### Using curl

- Send a get request to `http://localhost:3978/api/notify` to proactively message users from the bot.

   ```bash
    curl get http://localhost:3978/api/notify
   ```

- Using the Bot Framework Emulator, notice a message was proactively sent to the user from the bot.

### Using the Browser

- Launch a web browser
- Navigate to `http://localhost:3978/api/notify`
- Using the Bot Framework Emulator, notice a message was proactively sent to the user from the bot.

## Interacting with the bot

In addition to responding to incoming messages, bots are frequently called on to send "proactive" messages based on activity, scheduled tasks, or external events.

In order to send a proactive message using Bot Framework, the bot must first capture a conversation reference from an incoming message using `TurnContext.getConversationReference()`. This reference can be stored for later use.

To send proactive messages, acquire a conversation reference, then use `adapter.continueConversation()` to create a TurnContext object that will allow the bot to deliver the new outgoing message.

You can interact with this bot by sending it a message, or selecting a command from the command list. The bot will respond to the following strings.

1. **Show Welcome**
  - **Result:** The bot will send the welcome card for you to interact with
  - **Valid Scopes:** personal, group chat, team chat
2. **MentionMe**
  - **Result:** The bot will respond to the message and mention the user
  - **Valid Scopes:** personal, group chat, team chat
3. **MessageAllMembers**
  - **Result:** The bot will send a 1-on-1 message to each member in the current conversation (aka on the conversation's roster).
  - **Valid Scopes:** personal, group chat, team chat
4. **Respond**
  - **Result:** The bot will send a 1-on-1 message to each member in the current conversation (aka on the conversation's roster).
  - **Valid Scopes:** group chat, team chat
5. **Update Card**
  - **Result:** The bot will send a 1-on-1 message to each member in the current conversation (aka on the conversation's roster).
  - **Valid Scopes:** personal, group chat, team chat
6. **Delete Card**
  - **Result:** The bot will send a 1-on-1 message to each member in the current conversation (aka on the conversation's roster).
  - **Valid Scopes:** personal, group chat, team chat
7. **Notify All**
  - **Result:** This is actually out of chat. Navigate to `http://{host}:{port}/api/notify` .
  - **Valid Scopes:** personal, group chat, team chat

You can select an option from the command list by typing ```@alettah``` into the compose message area and ```What can I do?``` text above the compose area.

### Avoiding Permission-Related Errors

You may encounter permission-related errors when sending a proactive message. This can often be mitigated by using `MicrosoftAppCredentials.TrustServiceUrl()`. See [the documentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-howto-proactive-message?view=azure-bot-service-4.0&tabs=csharp#avoiding-401-unauthorized-errors) for more information.

## Deploy the bot to Azure

The bot is deployed into Azure using a CI/CD pipeline that is automatically triggered by commits to the master branch. For any contributions please ensure you clone the master branch and create a feature branch which you can name as ``feature/{featurename}``. Once done with your changes and tested locally, please pull any new changes that might have been added post your branching and created a new pull request.

## What needs to be done

You can interact with this bot by sending it a message, or selecting a command from the command list. The bot will respond to the following strings.

1. **Send dynamic custom messages**
  - **Behaviour:** The bot currently sends a static, hardcoded message and this needs to be changed so that the message to be sent can be passed in to the code.
  - **Suggested change:** Update/refactor `NotifyController.cs` to handle POST message that takes in custom message as part of the body and use the `BotCallback` delegate to send that custom message  instead of the static hard coded one.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/dotnet/csharp/methods)
2. **Respond to thread**
  - **Behaviour:** If no previous message has been sent before, the bot should send a new message, if a previous message has been sent before, the bot should respond to that message and create a new thread, or if multiple messages have been sent before the bot should respond to the existing thread.
  - **Suggested change:** Replace/refactor `SendActivityAsync(...)` in `NotifyController.cs`  to `ReplyToActivityAsync(...)`.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.connector.conversationsextensions.replytoactivityasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Connector_ConversationsExtensions_ReplyToActivityAsync_Microsoft_Bot_Connector_IConversations_System_String_System_String_Microsoft_Bot_Connector_Activity_System_Threading_CancellationToken_)
3. **Make State Persistent**
  - **Behaviour:** The conversation references are added to an array, however once the execution is complete these are lost. To persist conversation references across programs executions we will need a more persistent state store.
  - **Suggested change:** Create a state store and persist the collection of conversation references to that state store, an Azure Blob storage can work for this.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0)
4. **Send messages selectively**
  - **Behaviour:** The bot messages all conversation references in the array every time the notify operation is triggered. The more ideal scenario would be to send relevant messages to relevant channels by way of some correlation mechanism.
  - **Suggested change:** Find a way to incorporate a list of alerts a particular conversation reference want to subscribe to when the state is persisted. When those a triggered find all conversation references that are subscribed to them and send them the message.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0)
5. **Improve UI/UX**
  - **Behaviour:** Currently the bot sends messages in plain text. For improved user experience the plain text should be upgraded to for a better look and feel.
  - **Suggested change:** The plaintext messages should be sent as cards that have buttons which the user can click for the next actions. The cards can also have images to look more appealing and inviting.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-design-user-experience?view=azure-bot-service-4.0)
6. **Add natural language understanding**
  - **Behaviour:** Currently the bot is dumb and limited to a few preconfigured commands.
  - **Suggested change:** Add more commands to make bot more robust then add a LUIS component.
  - **Ref:** [Documentation](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-adaptive-dialog-introduction?view=azure-bot-service-4.0)

You can select an option from the command list by typing ```@alettah``` into the compose message area and ```What can I do?``` text above the compose area.

## Further reading

- [How Microsoft Teams bots work](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics-teams?view=azure-bot-service-4.0&tabs=javascript)
- [Bot Framework Documentation](https://docs.botframework.com)
- [Bot Basics](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [Send proactive messages](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-howto-proactive-message?view=azure-bot-service-4.0&tabs=js)
- [continueConversation Method](https://docs.microsoft.com/en-us/javascript/api/botbuilder/botframeworkadapter#continueconversation)
- [getConversationReference Method](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/turncontext#getconversationreference)
- [Activity processing](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Azure Bot Service Introduction](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Azure Bot Service Documentation](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [.NET Core CLI tools](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure Portal](https://portal.azure.com)
- [Language Understanding using LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [Channels and Bot Connector Service](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)
