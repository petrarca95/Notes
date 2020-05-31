Webhooks provide a way to configure Bitbucket Cloud to make request to a server or external service whenever certain events occur in Bitbucket

### Webhook is made up of:
- A subject – the resource that generates the events. Currently, this resource is the repository where you create the webhook.
- One or more events – the default event is a repository push, but you can select multiple events that can trigger the webhook.
- A URL – the endpoint where you want Bitbucket to send the event payloads when a matching event happens.

#### Two parts to getting a webhook to work:
1) creating a webhook
2) triggering a webhook

After you create a webhook for an event, every time that event occurs, Bitbucket sends a payload request that describes the event to the specified URL. Thus, you can think of webhooks as a kind of notification system.


### Common use for webhooks
-everytime a user pushes a commit in a repo, you may want to notify a CI server to start a build
