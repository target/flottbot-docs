+++
title = "Discord"
weight = 3
+++

## Run Your Bot On Discord

The Flottbot framework also supports Discord as a second remote chat host.

## 1. Setup Your Discord Bot

### Create your bot as an app

1. Navigate to the [Applications page](https://discordapp.com/developers/applications/me) and click on the [New App](https://discordapp.com/developers/applications/me/create) button.
2. Enter your bot's name under the `APP NAME *` section.
3. Click create the `Create App` button on the bottom right.

### Add your bot to your server

1. Navigate to the [Applications page](https://discordapp.com/developers/applications/me) and select one of your bot applications.
2. In the App Details section, copy the `Client ID` value.
3. Replace the `CLIENTID` in the following link with your bot's client ID from step 2: `https://discordapp.com/oauth2/authorize?client_id=CLIENTID&scope=bot`
4. Go to the link you generated, and select a server from the drop-down menu. **You must have the Manage Server permission to add a bot to a server!** If no servers appear, you may need to log in.

## 2. Configure Your Bot
Edit the following lines in `/config/bot.yml` to look like:

```yaml
chat_application: discord
discord_token: ${DISCORD_TOKEN}
```

Write some rules for your bot.

Now, you'll need to export your Discord bot token as an environment variable:

```sh
export DISCORD_TOKEN=xxxxxxxxxxxxxxxxxx.xxxxxx.xxxxxxxxx.xxxxxxxxxx
```

Now run your bot using docker with the following command (make sure docker is up and running):

```sh
docker run --rm --name mybot --env DISCORD_TOKEN=$DISCORD_TOKEN -v "$PWD"/config:/config target/flottbot:latest /flottbot
```

Your bot should now be online in the Discord server where you configured your bot.
