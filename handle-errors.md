---

copyright:
  years: 2018, 2023
lastupdated: "2023-08-25"

subcollection: watson-assistant

---

{{site.data.keyword.attribute-definition-list}}

# Providing options when a question or request can't be answered
{: #handle-errors}

No matter how well your assistant is designed, sometimes customers can run into problems getting it to understand them or do what they want. Your assistant can automatically help customers recover from many such situations, and you can configure how it responds.
{: shortdesc}

## How error conditions are handled
{: #handle-errors-how-error-conditions-handled}

There are several kinds of error situations your assistant might need to recover from:

- Your assistant cannot understand your customer's request
- Your customer does not provide a valid response to a question
- Your customer asks to talk to a live agent

Your assistant can detect error conditions and give customers the chance to correct them. In addition, the built-in *Fallback* action provides a way to automatically connect customers to a live agent if they need more help.

### When the assistant can't understand your customer's request
{: #no-action-matches}

When you build actions, you train your assistant on what your customers might ask for. The **Customer starts with** section of each action provides examples of customer input that trigger the action; the assistant uses natural language processing to recognize customer input that is similar to these examples. This happens at the beginning of the conversation, or after any action has completed and the assistant is ready for another action.

But you can't anticipate every possible request, so sometimes customers will send input that your assistant can't match to any action. This might happen because the input isn't phrased in a way that the assistant can understand; it can also happen if customers ask for things that your assistant isn't designed to handle.

Unrecognized input of this sort triggers the built-in *No action matches* action. To see how this action works, click **Set by assistant** in the list of actions, and then click *No action matches*.

![No action matches built-in action](images/no-action-matches.png){: caption="No action matches built-in action" caption-side="bottom"}

By default, this action has two steps, each step conditioned on the *No action matches count* session variable. This is a built-in variable that is automatically incremented with each consecutive unrecognized input. Therefore, the behavior of the *No action matches* action differs depending on how many times in a row the user has said something the assistant didn't understand:

- For the first three unrecognized messages, step 1 executes. This step simply outputs a message saying that the assistant did not recognize the user's input, and asking the user to try again. You can edit this step to change the message, or modify the step condition to change how many times the assistant responds with this message.

    When the user sends input that successfully triggers an action, the *No action matches count* session variable is reset to 0.

- If the user tries more than three times but the assistant still doesn't understand, step 2 executes. Step 2 calls the *Fallback* action, which offers other options like connecting to a live agent. For more information about the *Fallback* action, see [Editing the fallback action](#fallback-action).

You can edit the *No action matches* action just as you can any other action. This includes changing the existing steps and adding or deleting steps. Note that if you change the *No action matches* action, you might accidentally break your assistant's ability to recover from errors in the conversation. If this happens, you can recreate the default steps based on the information in this section.
{: tip}

You can set how often customers are routed to *No action matches* by changing a global setting for actions. [IBM Cloud]{: tag-ibm-cloud}

This setting is a beta feature that is available for evaluation and testing purposes on IBM Cloud only.
{: beta}

1. From the **Actions** page of the assistant, click **Global settings** ![Gear icon](../../icons/settings.svg).

1. On the **Clarifying questions** tab, you can edit the **No action matches** section. The choices are:

   - Rarely (default)
   - Sometimes
   - More often

#### Adding examples of unsupported input
{: #no-action-matches-add-examples}

By default, the *No action matches* action is triggered only when the user's input does not match any defined action.

If there are certain user requests that you can anticipate, but that your assistant does not support, you can add these requests as example phrases in the **Customer starts with** section of the *No action matches* action. Adding example phrases helps to ensure that these requests are sent directly to the *No action matches* action rather than triggering a different action by mistake. You can also upload or download examples phrases in a comma-separated value (CSV) file, just like you can when building your own actions. For more information on uploading or downloading example phrases, see [Adding more examples](/docs/watson-assistant?topic=watson-assistant-understand-questions#understand-questions-adding-more-examples).

### When your customer gives invalid answers
{: #step-validation}

When a step in an action asks your customer to answer questions or provide additional information, the assistant expects a particular response type, such as a number, date, or text string. (For more information about customer response types, see [Collecting information from your customer](/docs/watson-assistant?topic=watson-assistant-collect-info).) The assistant checks the customer's response to make sure it fits the expected response type; this process is called *validation*.

For most customer response types, the assistant is able to understand valid responses provided in a variety of formats. For example, for a time value, `2:15 PM` and `a quarter past two in the afternoon` are both acceptable. But if the user provides a value that the assistant cannot interpret as matching the expected response type (for example, a response of `purple` when asked for a number), a validation error results.

When there is a validation error, the assistant asks the customer to try again. By default, the assistant allows three attempts to provide a valid response.
After the third attempt, another invalid attempt triggers the *Fallback* action, which offers other options like connecting to a live agent. For more information about the *Fallback* action, see [Editing the fallback action](#fallback-action).

#### Customizing validation for a response
{: #customize-validation}

When you edit a step that expects a customer response, you can customize how validation errors are handled. 

1. Click **Edit validation** to see the validation options.

1. For all customer responses except *free text*, you can customize the following options:

   - In the **Response 1** field, specify the text of the message the assistant sends when the customer's response doesn't match the expected response type. For example, the default validation error message for a numeric value is `I didn't catch that. Enter a number.` You might want to customize this message to be more specific, such as `Enter the number of people in your group.`
   
   - If you want to use several validation responses, click **Add Response** to add more validation messages. The number of validation responses you can enter depends upon the value in the **If attempts exceed** field.

   - In the **If attempts exceed** field, click **`+`** or **`-`**, or directly edit the number, to change how many consecutive attempts the customer can make before the *Fallback* action is triggered. Or, if you enabled [response modes](/docs/watson-assistant?topic=watson-assistant-action-response-modes), you can use the number of step validation attempts from the response mode that you are using.
   
   - Select **Repeat Assistant Says response** if you want the customer to see or hear the original question. For example, following a validation message of `I’m sorry, please choose a day when we are open`, the assistant repeats `When do you want to visit? We are open Monday through Friday`.
   
For date, time, and numeric customer responses, you can customize the validation to check for a specific answer, such as a range of dates or a limited currency amount. Each choice is optional so that you can build a validation specific to the response.

| Response type | Validation choices |
| --- | --- |
| Number | Minimum, maximum |
| Date | After date, before date, specific days of the week |
| Time | Start time, end time |
| Currency | Minimum, maximum |
| Percentage | Minimum, maximum |
{: caption="Validation choices" caption-side="top"}

### When your customer asks to speak to a live agent
{: #fallback-human-agent}

At any point in the conversation, your customer might ask to speak to a live agent. The built-in *Fallback* action is preconfigured with example input that detects such requests; you can edit the **Customer starts with** section of the *Fallback* action to add more examples. You can also upload or download examples phrases in a comma-separated value (CSV) file, just like you can when building your own actions. For more information on uploading or downloading example phrases, see [Adding more examples](/docs/watson-assistant?topic=watson-assistant-understand-questions#understand-questions-adding-more-examples).

## Editing the fallback action
{: #fallback-action}

The built-in *Fallback* action is automatically provided with each assistant and cannot be deleted. However, you can edit the *Fallback* action to modify the conversation your users have with the assistant when errors occur. For example, you might want to add steps or modify step conditions to provide more control over how specific error conditions are handled.

To edit the *Fallback* action, click **Set by assistant** in the list of actions, and then click *Fallback*.

![Fallback built-in action](images/fallback-action.png){: caption="Fallback built-in action" caption-side="bottom"}

Whenever the *Fallback* action is triggered, the assistant also sets a value for the *Fallback reason* session variable. This value indicates what kind of situation led to the *Fallback* action being triggered. By default, this variable can have one of five values:

- *Step validation failed*: The customer repeatedly gave answers that were not valid for the expected customer response type.
- *Agent requested*: The customer directly asked to be connected to a live agent.
- *No action matches*: The customer repeatedly made requests or asked questions that the assistant did not understand.
- *Danger word detected*: The customer uses words or phrases that match the *Connect to agent* step in the *Trigger word detected* action. 
- *Profanity detected*: The customer repeatedly used words or phrases that match the *Show warning* step in the *Trigger word detected* action.

For more information about the *Trigger word detected* action, see [Detecting trigger words](/docs/watson-assistant?topic=watson-assistant-trigger-words).

The *Fallback* action defines five conditional steps, one for each possible value of the *Fallback reason* variable. Each step sends a message to the customer, based on the error condition, and then uses the *Connect to agent* feature to transfer the conversation to a live agent. (For more information about this feature, see [Connecting to a live agent](/docs/watson-assistant?topic=watson-assistant-human-agent).) You can modify these steps if you want to handle an error condition in a different way.
