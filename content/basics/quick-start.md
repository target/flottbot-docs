+++
title = "Quick Start"
weight = 1
+++

## Run Your Bot Locally

Ensure Docker is installed and running (highly recommended).

1. Create a directory structure to house your bot's config:

   ```sh
   mkdir config
   cd config
   mkdir rules
   ```

   Create the following files in the specified paths:

   `config/bot.yml`

   ```yaml
   name: mybot
   cli: true
   ```

   `config/rules/hello.yml`

   ```yaml
   # metadata
   name: hello
   active: true
   respond: hello
   format_output: "what's up?" # message to send to your user when they say hello
   direct_message_only: false
   help_text: hello
   include_in_help: true
   ```

2. `cd` into the directory holding the `config` you just created.

3. Run and exec into the bot's container: `docker run -it --rm --name mybot -v "$PWD"/config:/config target/flottbot:latest /bin/sh -c "./flottbot"`

_\* Note: Alternatively, you can download the binary for your operating system from the [flottbot release page](https://github.com/target/flottbot/releases) into the cloned repository's directory and execute it there. Basically, just replace step #3 from above with executing the binary, i.e. `./flottbot`._

The bot will start locally and you can interact with it via command line. Hit <kbd>Enter</kbd>, type `hello` and hit <kbd>Enter</kbd> again.

You should see your bot reply as follows:

```sh
mybot> what's up?
```

Congratulations! You just interacted with your bot via CLI mode. As you saw, you set up your first rule responds to `hello` (see rule in `config/rules/hello.yml`).

### About CLI Mode

CLI mode runs your bot locally on your machine and doesn't connect to any chat services. It's great for testing simple rules while you are building out your bot. Just keep in mind that there is no concept of rooms/channels when interacting with your bot in CLI mode.

You can disable CLI mode at any time by modifying your `/config/bot.yml` file, setting the `cli: true` to `cli: false`.

### Supported Configuration Formats

Although we recommend using `YAML`, we are using [spf13/viper](https://github.com/spf13/viper) which allows you to use multiple formats, including JSON, TOML, YAML, HCL, and Java properties files. See the [config-example](https://github.com/target/flottbot/tree/master/config-example) directory in the flottbot project for some examples.
