+++
title = "Examples"
weight = 3
+++

## Hello

This rule will simply listen for the word "hello", and respond with a greeting.

```yaml
# metadata
name: hello
active: true # activate rule

# trigger & arguments
respond: hello
args:
# no required args, as we're just looking for the match and nothing else.

# actions
actions: # this rule takes no actions, just collects and responds with whats laid out in the format_output section

# response
format_output: "what's up?" # message to send to your user when they say hello
direct_message_only: false # allow messaging inside channels
output_to_rooms: # this is an array of rooms/channels you want messages to be sent into.
  - mychannel # EDIT this (Slack channel you want the bot to listen & respond in)

#help
help_text: hello # help/usage text for the echo rule
include_in_help: true # see help_text in help message
```

## Joke

This rule will listen for the word "joke", and respond with a setup and a punchline from an open source joke API. See the syntax for the GET HTTP request in this rule.

```yaml
name: joke
active: true # whether the rule is active
respond: joke
args:
# no required args, as we're just looking for the match and nothing else
reaction: eyes
actions:
  - name: joke http request
    type: GET
    url: https://08ad1pao69.execute-api.us-east-1.amazonaws.com/dev/random_joke # url to talk to
    expose_json_fields:
      setup: '.setup'
      punchline: '.punchline'
    update_reaction: white_check_mark
direct_message_only: false
format_output: "${setup}\n\n${punchline}"
help_text: joke # help/usage text
include_in_help: true # hide it from help, because you only want admins to have jokes
```

## Number

This rule will listen for the word "number", run a ruby script `random.rb` that outputs a random number between 0-100 and make an http GET request to an API that returns a fun fact about that random number.

`random.rb` contents:

```ruby
random = rand(1..100)
puts random
```

```yaml
name: number
active: true # whether the rule is active
respond: number
args:
# no required args, as we're just looking for the match and nothing else
actions:
  - name: random number generator script
    type: exec
    cmd: ruby config/scripts/random.rb
  - name: number facts http request
    type: GET
    url: http://numbersapi.com/${_exec_output}?json # url to talk to
    expose_json_fields:
      number: '.number'
      text: '.text'
direct_message_only: false
format_output: "Fun fact about ${number}:\n\n${text}"
help_text: number # help/usage text
include_in_help: true # hide it from help, because you only want admins to have jokes
```

## Schedule

This rule will run on a schedule and message a specific channel/user specified every 10 seconds(dont leave this running long!).

```yaml
# meta
name: schedule
active: true

# trigger and args
schedule: '@every 10s'
# example working cron specs:
# - 0 * * * * (at minute 0)
# - @every 1m
# - @every 10s
# example breaking cron specs:
# - */1 * * * * (at every minute)

format_output: "The scheduled rule is running."

output_to_rooms:
  - "bot-dev"

output_to_users:
  - "jane.doe@target.com"

# help
include_in_help: false
```

## FOTD

This rule will run on a schedule and post a fact about the current day scraped from wikipedia to the #bot-dev channel every hour on the hour.

`random.sh` contents:

```bash
echo $RANDOM % $1 + 1 | bc
```

```yaml
# meta
name: fact of the day
active: true

# trigger and args
schedule: '@every 1h'
# actions
actions:
  - name: today in history http request
    type: GET
    url: https://history.muffinlabs.com/date
    expose_json_fields:
      num_of_events: |-
        {{len .data.Events}}
  - name: random number based on number of events from API
    type: exec
    cmd: bash config/scripts/random.sh ${num_of_events}
  - name: today in history http request
    type: GET
    url: https://history.muffinlabs.com/date
    expose_json_fields:
      date: '.date'
      year: |-
        {{ range $index, $element := .data.Events }}{{ if eq $index ${_exec_output} }}{{.year}}{{ end }}{{ end }}
      event: |-
        {{ range $index, $element := .data.Events }}{{ if eq $index ${_exec_output} }}{{.text}}{{ end }}{{ end }}


# response
format_output: "On ${date} in the year ${year}: ${event}"
direct_message_only: false
output_to_rooms:
  - "bot-dev"

# help
include_in_help: false
```
