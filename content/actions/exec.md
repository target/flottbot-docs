+++
title = "exec"
weight = 2
toc = true
+++

Exec actions allow rules to execute scripts in any language. These scripts will live under the `config/scripts` directory and will obviously have to be written by the developer. It is important to note that utilization of scripts **require using a language-specific flottbot images**. We currently provide two images other than the basic alpine based image for ruby and golang:

* [target/flottbot](https://hub.docker.com/r/target/flottbot) - Alpine image and flottbot binary installed
  * see [Dockerfile](https://github.com/target/flottbot/blob/master/docker/Dockerfile)

* [target/flottbot:ruby](https://hub.docker.com/r/target/flottbot) - Alpine image, flottbot binary, and ruby v2.5 installed
  * see [Dockerfile](https://github.com/target/flottbot/blob/master/docker/Dockerfile.ruby)

* [target/flottbot:golang](https://hub.docker.com/r/target/flottbot) - Alpine image, flottbot binary, and golang v1.11 installed
  * see [Dockerfile](https://github.com/target/flottbot/blob/master/docker/Dockerfile.golang)

* [target/flottbot:python](https://hub.docker.com/r/target/flottbot) - Alpine image, flottbot binary, and python v3.7 installed
  * see [Dockerfile](https://github.com/target/flottbot/blob/master/docker/Dockerfile.python)

It is of course possible to custom build your own image by writing a Dockerfile which utilizes a language-specific image of your preference and installing the flottbot binary from our [github releases)(https://github.com/target/flottbot/releases).

The following example executes a ruby script that outputs 'Hello from Ruby!' to stdout. You can then return that return value to be used in a response. Again, you must use the `target/flottbot:ruby` image in order for you ruby scripts to execute successfully.

```yaml
actions: # actions to take when the rule triggered
  - name: sample script # name of action
    type: exec # type of action
    cmd: ruby config/scripts/main.rb # command to send container to run your script.
    timeout: 30 # custom 30 second timeout for an exec rule. 10 Seconds is the default.
    update_reaction: white_check_mark # If you set an emoji reaction when the rule was matched, you can include this field to update the reaction when the script completes. You can use templating in this field as well for advanced usage.
```

The `main.rb` script looks like:

```ruby

puts 'Hello from Ruby!'
```

Here's the same example but in `python`:

```yaml
actions: # actions to take when the rule triggered
  - name: sample script # name of action
    type: exec # type of action
    cmd: python config/scripts/main.py # command to send container to run your script.
    timeout: 30 # custom 30 second timeout for an exec rule. 10 Seconds is the default.
```

The `main.py` script looks like:

```python
#!/usr/bin/env python3

print("Hello from Python!")
```

### Error handling

Sadly the world isn't perfect, so you may want to handle some edge cases and catch errors. By default, flottbot will return the following error if you don't toss a message to stdout `Error while executing script for action '<action name>'. See bot admin for more information."`. You can define a more custom error message by printing to stdout and the user will receive that as the error. For example in:

```python
#!/usr/bin/env python3

print("Hello!")
raise NameError("I broke")
```

The user will receive `Hello!` from the bot. Flottbot will also log the error to it's own log, so for the above script you as the bot owner will see something like:

```json
{"level":"error","msg":"Exec rule for action 'sample script' failed with exit status 1: Traceback (most recent call last):\n File "config/scripts/main.py", line 6, in \u003cmodule\u003e\n raise NameError("I broke")\nNameError: I broke\n","time":"2018-02-07T22:04:14-06:00"}
```

### Timeouts

By default, each exec action has a timeout of 10 seconds. You can override this behavior by specifying a custom timeout within the action section of the `rules/<rule name>.yml` file. When a timeout is reached the user will receive a message of `Hmm, something timed out. Please try again.` and the bot log will receive a message of `Timeout reached, exec process for action '<action>' killed"`.
