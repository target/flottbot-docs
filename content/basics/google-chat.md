+++
title = "Google Chat"
weight = 3
+++

## Overview

The Google Chat remote only supports the Cloud Pub/Sub. This remote consumes a Pub/Sub subscriber that pushes and pulls events from Google Chat API. [Google Chat API is only available to Google Workspace users](https://developers.google.com/chat/quickstart/gcf-app#google_chat_api_is_only_available_to_google_workspace_users).

## Known Limitations

Google Chat doesn't expose every message to bots. As a result, Flottbot's `hear` rules will not work. Insted, bots can only be referenced with @ mentions or direct communication.

## Configure Google Chat

To enable Google Chat you will need to create a Google Cloud project. See Google's documentation for [Using Cloud Pub/Sub as an endpoint for your bot](https://developers.google.com/chat/how-tos/pub-sub).

Once you've completed the [create a Pub/Sub topic](https://developers.google.com/chat/how-tos/pub-sub#create_a_pubsub-enabled_google_cloud_project) step, store the GCP project and subscription IDs in your config.

- `google_chat_project_id` - GCP project ID
- `google_chat_subscription_id` - Cloud pub/sub subscription ID

Once you've completed the [enable the Google Chat API](https://developers.google.com/chat/how-tos/pub-sub#enable_the_google_chat_api) step, download the JSON file containing your bot's key. In your bot's config, set to the path of this downloaded credentials file. For more information in obtaining the bot key, follow this [creating and using a service account](https://developers.google.com/chat/how-tos/service-accounts#creating_and_using_a_service_account) guide.

- `google_chat_credentials` - path to service account JSON credentials file

Example configuration:

```yaml
name: flottbot # EDIT this (name of your bot)

chat_application: google_chat
google_chat_credentials: config/service-account.json
google_chat_project_id: flottbot
google_chat_subscription_id: flottbot-sub
```
