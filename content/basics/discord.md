+++
title = "Discord"
weight = 3
+++

## Run Your Bot On Discord

The flottbot framework also supports Discord as a remote chat network.

## Setup Your Discord Bot

### Create your bot as an app

1. Navigate to the [Applications page](https://discordapp.com/developers/applications/me) 
1. Click on the `New Application` button
1. Enter your bot's name on the next screen
1. Click create the `Create` button on the bottom right

### Add your bot to your server

1. Navigate to the [Applications page](https://discordapp.com/developers/applications/me)
1. Select one of your bot applications
1. Click on `OAuth2` in the side navigation under "Settings"
1. Check the checkbox for `Bot` in the "Scopes" section
1. Copy the link it generated and navigate to it in your browser
1. Select a server from the drop-down menu. **You must have the Manage Server permission to add a bot to a server!** If no servers appear, you may need to log in.

## Configure Your Bot

First you will need your **bot token** and **server id**. For the bot token, follow these steps:

1. Navigate to the [Applications page](https://discordapp.com/developers/applications/me)
1. Select one of your bot applications
1. Click on `Bot` in the side navigation under "Settings"
1. Use `Click to Reveal Token` in the "Build-A-Bot" section to see your token

To get your server id, [check Discord's official knowledgebase article](https://support.discordapp.com/hc/en-us/articles/206346498-Where-can-I-find-my-User-Server-Message-ID-)

Now that you have both your bot token and server id, edit the following lines in `/config/bot.yml` to look like:

```yaml
chat_application: discord
discord_token: ${DISCORD_TOKEN}
discord_server_id: ${DISCORD_SERVER_ID}
```

Write some rules for your bot.

Now, you'll need to export your Discord bot token and server id values as environment variables:

```sh
export DISCORD_TOKEN=xxxxxxxxxxxxxxxxxx.xxxxxx.xxxxxxxxx.xxxxxxxxxx
export DISCORD_SERVER_ID=###############
```

Now run your bot using docker with the following command (make sure docker is up and running):

```sh
docker run --rm --name mybot --env DISCORD_TOKEN=$DISCORD_TOKEN -v "$PWD"/config:/config target/flottbot:latest /flottbot
```

Your bot should now be online in the Discord server where you configured your bot.
