# Adding API Endpoints to status page

API endpoints should be added to the status page for others to have a more easy way to track information such as uptime and planned maintenance.

## Motivation

Upptime currently provides JSON files in the GitHub repository that can be used to display a monitor's uptime using shields.io for rendering the data.  
While this may serve for a majority of the people does it lack features and may also suffer from possible rate limits as the githubusercontent.com domain is - to my knowledge - rate limited.

Adding an api endpoint to the status page could bypass this limitation while also providing a more extensible way for people to integrate the status page into their work (i.e. Discord Bot commands or alike).

## Structure

### Paths

The following Rest API paths are suggested:

```http
GET /api/status
GET /api/status/<monitor>
GET /api/maintenances
GET /api/maintenances/<monitor>
GET /api/incidents
GET /api/incidents/<monitor>
GET /api/incident/<id>
```

#### `/api/status`

Returns the Status of all tracked monitors.

Accepts a query parameter called `period` that when provided with a valid value would display the status of the monitors for the provided period.  
Allowed values are `24h`, `7d`, `30d`, `1y` and `all`. Defaults to `7d`.

----

#### `/api/status/<monitor>`

Returns the Status of the provided `<monitor>`.

The provided `<monitor>` has to be a matching slug used by the status page.

Accepts a query parameter called `period` that when provided with a valid value would display the status of the monitor for the provided period.  
Allowed values are `24h`, `7d`, `30d`, `1y` and `all`. Defaults to `7d`.

----

#### `/api/maintenances`

Returns any planned or ongoing maintenance tasks for all tracked monitors.

----

#### `/api/maintenances/<monitor>`

Returns any planned or ongoing maintenance for the provided `<monitor>`.

The provided `<monitor>` has to be a matching slug used by the status page.

----

#### `/api/incidents`

Returns all past and ongoing incidents, including maintenance, for all tracked monitors.

Accepts two query parameters:

- `type` to filter by incident type. Acceptable values are `maintenance`, `degraded` and `down`. Defaults to displaying all.
- `limit` to limit the amount of entries to be returned. Has to be a whole positive number of at least 1. Defaults to no limit.

----

#### `/api/incidents/<monitor>`

Returns all past and ongoing incidents, including maintenance, for the provided `<monitor>`

The provided `<monitor>` has to be a matching slug used by the status page.

Accepts two query parameters:

- `type` to filter by incident type. Acceptable values are `maintenance`, `degraded` and `down`. Defaults to displaying all.
- `limit` to limit the amount of entries to be returned. Has to be a whole positive number of at least 1. Defaults to no limit.

----

#### `/api/incident/<id>`

Returns information about one specific incident of the provided `<id>`.

The provided `<id>` has to match an existing GitHub Issue on the Status page's GitHub Repository.

### Responses

Each request should return a `application/json` response.

#### `/api/status`

```json
[
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "status": "up",
    "uptime_percentage": 100.0,
    "average_response_time": 50,
    "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/monitor-1/response-time-week.png"
  },
  {
    "id": "monitor-2",
    "name": "Monitor 2",
    "url": "https://monitor2.example.com",
    "status": "down",
    "uptime_percentage": 71.3,
    "average_response_time": -1,
    "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/monitor-2/response-time-week.png"
  }
]
```

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `status`: The status of this monitor. Can be `up`, `degraded` or `down`.
- `uptime_percentage`: The overall Uptime of this monitor as a percentage.
- `average_response_time`: The time in milliseconds it took for the tracked URL to respond. Returns `-1` if the site is currently down.
- `graph`: Direct image URL to the graph displayed in the Status page overview.

----

#### `/api/status/<monitor>`

```json
{
  "id": "monitor-1",
  "name": "Monitor 1",
  "url": "https://monitor1.example.com",
  "status": "up",
  "uptime_percentage": 100.0,
  "average_response_time": 50,
  "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/monitor-1/response-time-week.png"
}
```

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `status`: The status of this monitor. Can be `up`, `degraded` or `down`.
- `uptime_percentage`: The overall Uptime of this monitor as a percentage.
- `average_response_time`: The time in milliseconds it took for the tracked URL to respond. Returns `-1` if the site is currently down.
- `graph`: Direct image URL to the graph displayed in the Status page overview.

----

#### `/api/maintenances`

```json
[
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "title": "Planned maintenance",
    "status": "scheduled",
    "start_time": "2023-04-30T01:00:00+02:00",
    "end_time": "2023-04-30T06:00:00+02:00",
    "expect_down": false,
    "expect_degraded": true,
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-30T01:00:00+02:00",
        "content": "The service will undergo maintenance to fix and update things."
      }
    ]
  },
  {
    "id": "monitor-2",
    "name": "Monitor 2",
    "url": "https://monitor2.example.com",
    "title": "Planned maintenance",
    "status": "scheduled",
    "start_time": "2023-04-30T01:00:00+02:00",
    "end_time": "2023-04-30T06:00:00+02:00",
    "expect_down": false,
    "expect_degraded": true,
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-30T01:00:00+02:00",
        "content": "The service will undergo maintenance to fix and update things."
      }
    ]
  }
]
```

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `title`: The title of the maintenance.
- `status`: The status of this maintenance. Can be `scheduled`, `active` or `completed`.
- `start_time`: ISO Formatted date and time of when the maintenance will start.
- `end_time`: ISO formatted date and time of when the maintenance will end.
- `expect_down`: Whether downtime is to be expected for the monitor.
- `expect_degraded`: Whether degraded performance is to be expected for the monitor.
- `issue`: URL pointing to the related GitHub issue for this maintenance.
- `messages`: Array containing JSON objects for send messages in the tracked issue.
  - `author`: The GitHub Username of the writer of this message.
  - `date`: ISO formatted date and time of when this message has been send.
  - `content`: The actual message that has been send.

----

#### `/api/maintenances/<monitor>`

```json
{
  "id": "monitor-1",
  "name": "Monitor 1",
  "url": "https://monitor1.example.com",
  "title": "Planned maintenance",
  "status": "scheduled",
  "start_time": "2023-04-30T01:00:00+02:00",
  "end_time": "2023-04-30T06:00:00+02:00",
  "expect_down": false,
  "expect_degraded": true,
  "issue": "https://github.com/Example/Staus/issue/1",
  "messages": [
    {
      "author": "Example",
      "date": "2023-04-30T01:00:00+02:00",
      "content": "The service will undergo maintenance to fix and update things."
    }
  ]
}
```

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `title`: The title of the maintenance.
- `status`: The status of this maintenance. Can be `scheduled`, `active` or `completed`.
- `start_time`: ISO Formatted date and time of when the maintenance will start.
- `end_time`: ISO formatted date and time of when the maintenance will end.
- `expect_down`: Whether downtime is to be expected for the monitor.
- `expect_degraded`: Whether degraded performance is to be expected for the monitor.
- `issue`: URL pointing to the related GitHub issue for this maintenance.
- `messages`: Array containing JSON objects for send messages in the tracked issue.
  - `author`: The GitHub Username of the writer of this message.
  - `date`: ISO formatted date and time of when this message has been send.
  - `content`: The actual message that has been send.

---

#### `/api/incidents`

```json
[
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "type": "maintenance",
    "title": "Planned maintenance",
    "status": "planned",
    "start_time": "2023-04-30T01:00:00+02:00",
    "end_time": "2023-04-30T06:00:00+02:00",
    "expect_down": false,
    "expect_degraded": true,
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-30T01:00:00+02:00",
        "content": "The service will undergo maintenance to fix and update things."
      }
    ]
  },
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "type": "down",
    "title": "Monitor 1 is down"
    "start_time": "2023-04-29T06:00:00+02:00",
    "end_time": "2023-04-29T06:25:00+02:00",
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-29T06:24:00+02:00",
        "content": "A temporary fix has been implemented. A future maintenance task will fix this permanently."
      },
      {
        "author": "Example",
        "date": "2023-04-29T06:00:00+02:00",
        "content": "Monitor 1 is experiencing an increased amount of 500 errors. We are investigating this."
      }
    ]
  }
]
```

Fields available in all incident types:

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `type`: The type of incident that happened. Can be `maintenance`, `degraded` or `down`.
- `title`: The title of the incident.
- `start_time`: ISO Formatted date and time of when the incident started.
- `end_time`: ISO formatted date and time of when the incident ended. Returns `null` if it is still active.
- `issue`: URL pointing to the related GitHub issue for this maintenance.
- `messages`: Array containing JSON objects for send messages in the tracked issue.
  - `author`: The GitHub Username of the writer of this message.
  - `date`: ISO formatted date and time of when this message has been send.
  - `content`: The actual message that has been send.

Fields only available for maintenance entries:

- `status`: The status of this maintenance. Can be `scheduled`, `active` or `completed`.
- `expect_down`: Whether downtime is to be expected for the monitor.
- `expect_degraded`: Whether degraded performance is to be expected for the monitor.

---

#### `/api/incidents/<monitor>`

```json
[
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "type": "maintenance",
    "title": "Planned maintenance",
    "status": "planned",
    "start_time": "2023-04-30T01:00:00+02:00",
    "end_time": "2023-04-30T06:00:00+02:00",
    "expect_down": false,
    "expect_degraded": true,
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-30T01:00:00+02:00",
        "content": "The service will undergo maintenance to fix and update things."
      }
    ]
  },
  {
    "id": "monitor-1",
    "name": "Monitor 1",
    "url": "https://monitor1.example.com",
    "type": "down",
    "title": "Monitor 1 is down"
    "start_time": "2023-04-29T06:00:00+02:00",
    "end_time": "2023-04-29T06:25:00+02:00",
    "issue": "https://github.com/Example/Staus/issue/1",
    "messages": [
      {
        "author": "Example",
        "date": "2023-04-29T06:24:00+02:00",
        "content": "A temporary fix has been implemented. A future maintenance task will fix this permanently."
      },
      {
        "author": "Example",
        "date": "2023-04-29T06:00:00+02:00",
        "content": "Monitor 1 is experiencing an increased amount of 500 errors. We are investigating this."
      }
    ]
  }
]
```

Fields available in all incident types:

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `type`: The type of incident that happened. Can be `maintenance`, `degraded` or `down`.
- `title`: The title of the incident.
- `start_time`: ISO Formatted date and time of when the incident started.
- `end_time`: ISO formatted date and time of when the incident ended. Returns `null` if it is still active.
- `issue`: URL pointing to the related GitHub issue for this maintenance.
- `messages`: Array containing JSON objects for send messages in the tracked issue.
  - `author`: The GitHub Username of the writer of this message.
  - `date`: ISO formatted date and time of when this message has been send.
  - `content`: The actual message that has been send.

Fields only available for maintenance entries:

- `status`: The status of this maintenance. Can be `scheduled`, `active` or `completed`.
- `expect_down`: Whether downtime is to be expected for the monitor.
- `expect_degraded`: Whether degraded performance is to be expected for the monitor.

----

#### `/api/incident/<id>`

```json
{
  "id": "monitor-1",
  "name": "Monitor 1",
  "url": "https://monitor1.example.com",
  "type": "down",
  "title": "Monitor 1 is down"
  "start_time": "2023-04-29T06:00:00+02:00",
  "end_time": "2023-04-29T06:25:00+02:00",
  "issue": "https://github.com/Example/Staus/issue/1",
  "messages": [
    {
      "author": "Example",
      "date": "2023-04-29T06:24:00+02:00",
      "content": "A temporary fix has been implemented. A future maintenance task will fix this permanently."
    },
    {
      "author": "Example",
      "date": "2023-04-29T06:00:00+02:00",
      "content": "Monitor 1 is experiencing an increased amount of 500 errors. We are investigating this."
    }
  ]
}
```

Fields available in all incident types:

- `id`: The slug used for the monitor.
- `name`: The name used to display the monitor as in the status page.
- `url`: The URL that is being tracked by the monitor.
- `type`: The type of incident that happened. Can be `maintenance`, `degraded` or `down`.
- `title`: The title of the incident.
- `start_time`: ISO Formatted date and time of when the incident started.
- `end_time`: ISO formatted date and time of when the incident ended. Returns `null` if it is still active.
- `issue`: URL pointing to the related GitHub issue for this maintenance.
- `messages`: Array containing JSON objects for send messages in the tracked issue.
  - `author`: The GitHub Username of the writer of this message.
  - `date`: ISO formatted date and time of when this message has been send.
  - `content`: The actual message that has been send.

Fields only available for maintenance entries:

- `status`: The status of this maintenance. Can be `scheduled`, `active` or `completed`.
- `expect_down`: Whether downtime is to be expected for the monitor.
- `expect_degraded`: Whether degraded performance is to be expected for the monitor.


## Failed requests

A failed request should return a non-200 status code with an error message explaining what went wrong. In addition should the body return a `application/json` response containing a copy of the status and response:

```json
{
  "code": 404,
  "message": "The provided monitor does not exist."
}
```

The following combination of status code and message should be considered for the following cases:

| Case                     | Code | Message                                                                                     |
|--------------------------|------|---------------------------------------------------------------------------------------------|
| Invalid Monitor slug     | 404  | The provided monitor does not exist.                                                        |
| Invalid Incident ID      | 404  | The provided incident ID does not exist.                                                    |

The following combination of status code and message should also be considered, if technically doable:

| Case                     | Code | Message                                                                                     |
|--------------------------|------|---------------------------------------------------------------------------------------------|
| Making too many requests | 429  | Too many requests have been made. Please wait {n} seconds before doing another request.[^1] |

[^1]: `{n}` would be replaced with the number of seconds the user has to wait. Response should also have Headers for the remaining time and a timestamp of the reset.

## Limitations and Difficulties

Adding this would require a lot of extra work for the upptime tools to work with. And given that some users may not want an API to be exposed could this also require a configuration option to enable/disable these things.

Understanding the status page right are paths such as `example.com/incident/1` handled on the fly with no actual page in the backend, which most likely could be applied to the API paths too.

The next open question is regarding whether there should be a rate-limiting of sorts in place (if doable using GitHub Pages itself) or not and other things such as caching values for faster and less resource-intensive responses.
