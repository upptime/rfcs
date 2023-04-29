# Adding API Endpoints to status page

API endpoints should be added to the status page for others to have a more easy way to track information such as uptime and planned maintenance.

## Motivation

Upptime currently provides JSON files in the GitHub repository that can be used to display a monitor's uptime using shields.io for rending the data.  
While this may serve for a majority of the people does it lack features and may also suffer from possible rate limits as the githubusercontent.com domain is - to my knowledge - rate limited.

Adding an api endpoint to the status page could bypass this limitation while also providing a more extensible way for people to integrate the status page into their work (i.e. Discord Bot command sor alike).

## Structure

### Paths

The following Rest API paths are suggested:

```http
GET /api/status
GET /api/status/<monitor>/status
GET /api/maintenances
GET /api/maintenances/<monitor>
GET /api/incidents
GET /api/incidents/<monitor>
GET /api/incident/<id>
```

- `/api/status` would return the Status of all tracked monitors.
- `/api/status/<monitor>` would return the Status of the provided `<monitor>` which has to be a matching slug.
- `/api/maintenances` would return planned schedules of all tracked monitors.
- `/api/maintenances/<monitor>` would return planned schedules of the provided `<monitor>` which has to be a matching slug.
- `/api/incidents` would return all past and current incidents - including maintenance -  for all tracked monitors.
- `/api/incidents/<monitor>` would return all past and current incidents - including maintenance - of the provided `<monitor>` which has to be a matching slug.
- `/api/incident/<id>` would return the information for an incident of the provided `<id>` which has to be a matching GitHub Issue ID.

Both `/api/status` and `/api/status/<monitor>` may accept a `period=<period>` query parameter that, when provided, returns the statistics for the provided time period, similar to how it already done in the status page right now.  
Acceptable values should be `24h`, `7d` (Default), `30d`, `1y` and `all`.

`/api/incident` and `/api/history/<monitor>` may accept 2 query parametrs:

- `type=<type>` to filter by specific event types. Allowed would be `maintenance`, `degraded` and `down`
- `limit=<limit>` to set how many entries should be returned at most.

### Responses

For `/api/status/<monitor>` should the following JSON body be returned:

```json
{
  "id": "slug",
  "name": "Slug",
  "status": "up",
  "uptime_percentage": 100.0,
  "average_response_time": 50,
  "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/slug/response-time-week.png"
}
```

- `id` returns the slug of the monitor.
- `name` returns the name of the monitor as defined in the upptime configuration.
- `status` returns the current status of the monitor. Values can be `up`, `down` or `degraded`
- `uptime_percentage` returns the overall percentage of the past uptime (Based on the provided period).
- `average_response_time` returns the average time in milliseconds the monitored site took to response (Based on the provided period).
- `graph` returns a URL pointing to the Graph used for the provided period.

Using `/api/status` would return a JSON Array containing the above JSON body with values for each tracked monitor.

An empty JSON Object would be returned for `/api/status/<monitor>` when an invalid `<monitor>` has been provided.

---

For `/api/maintenances/<monitor>` should the following JSON body be returned:

```json
{
  "id": "slug",
  "name": "Slug",
  "title": "Planned maintenance",
  "status": "planned",
  "start_time": "2023-04-2023T01:00:00+02:00",
  "end_time": "2023-04-2023T06:00:00+02:00",
  "expect_down": false,
  "expect_degraded": true,
  "issue": "https://github.com/Example/Staus/issue/1",
  "messages": [
    {
      "author": "Example",
      "date": "2023-04-2023T01:00:00+02:00",
      "content": "The service will undergo maintenance to fix and update things."
    }
  ]
}
```

- `id` returns the slug of the monitor.
- `name` returns the name of the monitor as defined in the upptime configuration.
- `title` returns the title used for the maintenance.
- `status` returns the current status of the maintenance. Possible returns are:
  - `pending` The maintenance has not yet started.
  - `active` The maintenance is currently ongoing.
  - `completed` The maintenance has been completed.
- `start_time` returns the ISO timestamp defined in the issue on when the maintenance will start.
- `end_time` returns the ISO timestamp defined in the issue on when the maintenance will end.
- `expect_down` returns a boolean on if downtime should be expected.
- `expect_degraded` returns a boolean on if degraded performance should be expected.
- `issue` returns the URL to the issue about this maintenance.
- `messages` returns an Array with all the messages that have been posted in the issue so far. Each entry would be a JSON object with the following content:
  - `author` containing the GitHub Username of who posted the message.
  - `date` containing an ISO formatted date of when the message was posted.
  - `content` containing the message itself.

Using `/api/maintenances` would return a JSON Array containing the above JSON body with values for each tracked monitor which has planned or ongoing maintenance.

An empty JSON Object would be returned for `/api/maintenance/<monitor>` and an empty JSON Array for `/api/maintenances` if no maintenances are planned or ongoing.

---

For `/api/incidents` should the following JSON body be returned:

```json
[
  {
    "id": "slug",
    "name": "Slug",
    "type": "maintenance",
    "title": "Planned maintenance",
    "status": "planned",
    "start_time": "2023-04-2023T01:00:00+02:00",
    "end_time": "2023-04-2023T06:00:00+02:00",
    "expect_down": false,
    "expect_degraded": true,
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-2023T01:00:00+02:00",
        "content": "The service will undergo maintenance to fix and update things."
      }
    ]
  }
]
```

Both maintenance and downtime/degraded performance share these values:

- `id` returns the slug of the monitor.
- `name` returns the name of the monitor as defined in the upptime configuration.
- `type` returns the type of incident. Can be `maintenance`, `down` or `degraded`.
- `title` returns the title used for the incident.
- `issue` returns the URL to the issue about this incident.
- `start_time` returns the ISO timestamp defined in the issue on when the incident started.
- `end_time` returns the ISO timestamp defined in the issue on when the incident endet. Returns `null` if it is ongoing.
- `messages` returns an Array with all the messages that have been posted in the issue so far. Each entry would be a JSON object with the following content:
  - `author` containing the GitHub Username of who posted the message.
  - `date` containing an ISO formatted date of when the message was posted.
  - `content` containing the message itself.

For maintenance, the following extra fields will be available:

- `status` returns the current status of the maintenance. Possible returns are:
  - `pending` The maintenance has not yet started.
  - `active` The maintenance is currently ongoing.
  - `completed` The maintenance has been completed.
- `expect_down` returns a boolean on if downtime should be expected.
- `expect_degraded` returns a boolean on if degraded performance should be expected.

For `/incidents/<monitor>` would the returned array only contain incidents linked to the provided `<monitor>` slug.

No incidents would return an empty JSON array.

---

For `/api/incident/<id>` would a JSON body the same as for `/api/incidents` be returned containing information for the provided `<id>`.

No matching incident would return an empty JSON object.

## Failed requests

A failed request (i.e. by accessing an invalid path) should result in a `application/json` body being returned with the following content.

```json
{
  "message": "Invalid Monitor 'monitor'"
}
```

- `message` returns a string containing the reason for the failed request

## Limitations and Difficulties

Adding this would require a lot of extra work for the upptime tools to work with. And given that some users may not want an API to be exposed could this also require a configuration option to enable/disable these things.

Understanding the status page right are paths such as `example.com/incident/1` handled on the fly with no actual page in the backend, which most likely could be applied to the API paths too.

The next open question is regarding whether there should be a rate-limiting of sorts in place (if doable using GitHub Pages itself) or not and other things such as caching values.
