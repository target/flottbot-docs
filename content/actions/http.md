+++
title = "HTTP"
weight = 1
toc = true
+++

## GET

This is an example of a HTTP GET request to an open source Pokemon API. Note that `expose_json_fields` helps access top-level and nested values inside the JSON response from your API using [Golang template syntax](https://golang.org/pkg/html/template/) and assigns them to the fields under `expose_json_fields` (in the example below, pokename, type, weight, etc.)

```yaml
actions:
  - name: pokemon http request
    type: GET
    url: http://pokeapi.co/api/v2/pokemon/${name}
    query_data:
    auth:
    expose_json_fields:
      pokename: '.name'
      type: |-
        {{ range .types }}
          - {{ .type.name -}}
        {{ end }}
      weight: '.weight'
      baseexp: '.base_experience'
      ability: |-
        {{ range .abilities }}
          - {{ .ability.name -}}
        {{ end }}
    timeout: 30 # custom 30 second timeout for an http rule. 10 Seconds is the default.
    update_reaction: white_check_mark # If you set an emoji reaction when the rule was matched, you can include this field to update the reaction when the http call completes.
```

## POST

This is an example of a HTTP POST request to the github API. The action below will create a Github issue. This action takes in a few arguments so the bot knows where to create the issue, what to call it, and who to assign the issue to. See again in `expose_json_fields` how you can assign response values to the exposed fields which can be accessed in later parts of your rule yaml.

```yaml
actions:
  - name: github issue http request
    type: POST
    url: https://api.github.com/v3/repos/${org}/${repo}/issues
    query_data:
      title: "${title}"
      body: "TODO: Fill in the deets.\n ACCEPTANCE CRITERIA:"
      assignees:
        - "${assignee}"
      labels:
        - "bug"
    custom_headers:
      Authorization: token ${GITHUB_API_TOKEN}
      Accept: 'application/vnd.github.v3+json'
      Content-Type: 'application/json'
    auth:
    expose_json_fields:
      title: '.title'
      url: '.html_url'
      assignees: '.assignee.login'
```

## Chained HTTP Requests

Here is an example of two HTTP GET requests chained together to produce an output containing information about the weather of a given location as well as its population.

```yaml
actions:
  - name: weather http request
    type: GET
    url: http://api.openweathermap.org/data/2.5/weather
    query_data:
      q: ${location}
      APPID: ${WEATHER_APPID}
      units: metric
    auth:
    expose_json_fields:
      temperature: '.main.temp'
      loc: '.name'
      desc: |-
        {{ range .weather }}
        {{ range $k, $v := . }}
        - {{- $v -}}
        {{ end }}
        {{ end }}
      country: '.sys.country'
  - name: location http request
    type: GET
    url: https://restcountries.eu/rest/v2/alpha/${country}
    expose_json_fields:
      flag_url: .flag
      full_name: '.name'
      population: 'printf "%0.f" .population'
      native_name: .nativeName
```

### Timeouts

By default, each http action has a timeout of 10 seconds. You can override this behavior by specifying a custom timeout in the `rules/<rule name>.yml` file.
