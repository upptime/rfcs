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
GET /api/monitor
GET /api/monitor/<monitor>
GET /api/incident
GET /api/incident/<id>
```

#### `/api/monitor`

Returns information of all currently tracked monitors including past incidents and planned maintenance.

**Query Parameters**  
- `range=<range>` (Optional): Sets the range of which to show data for. Acceptable values are `24h`, `7d`, `30d`, `1y` and `all`. Defaults to `7d` on no/invalid value.

**Response:**

```json
[
  {
    "monitor": {
      "slug": "monitor-1",
      "title": "Monitor 1",
      "url": "https://monitor1.example.com",
      "status": "up",
      "uptime": {
        "percentage": 100.0,
        "response_time": 50
      },
      "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/monitor-1/response-time-week.png"
    },
    "incidents": [
      {
        "id": 2,
        "title": "Emergency maintenance to fix 500 errors",
        "type": "maintenance",
        "status": "completed"
        "times": {
          "start": "2023-04-30T06:00:00+02:00",
          "end": "2023-04-30T06:25:00+02:00"
        },
        "url": "https://github.com/Example/Status/issue/2",
        "messages": [
          {
            "author": "ExampleUser",
            "date": "2023-04-29T06:15:00+02:00",
            "content": "Maintenance has been completed without any issues.",
            "link": "https://github.com/Example/Status/issue/1#issuecomment-123456713"
          },
          {
            "author": "ExampleUser",
            "date": "2023-04-29T06:01:00+02:00",
            "content": "The emergency maintenance as mentioned in the incident [#1](https://github.com/Example/Status/issue/1) has now started.",
            "link": "https://github.com/Example/Status/issue/1#issuecomment-123456712"
          }
        ],
        "maintenances": {
          "expect_down": true,
          "expect_degraded": false
        }
      },
      {
        "id": 1,
        "title": "Increased amount of 500 errors",
        "type": "incident",
        "status": "down",
        "times": {
          "start": "2023-04-29T06:00:00+02:00",
          "end": "2023-04-29T06:25:00+02:00"
        },
        "url": "https://github.com/Example/Status/issue/1",
        "messages": [
          {
            "author": "ExampleUser",
            "date": "2023-04-29T06:24:00+02:00",
            "content": "A temporary fix has been applied and we will prepare an emergency maintenance to fix this permanently.",
            "link": "https://github.com/Example/Status/issue/1#issuecomment-123456711"
          },
          {
            "author": "ExampleUser",
            "date": "2023-04-29T06:10:00+02:00",
            "content": "We have found the cause of the issue and will apply a temporary fix for it.",
            "link": "https://github.com/Example/Status/issue/1#issuecomment-123456710"
          },
          {
            "author": "ExampleUser",
            "date": "2023-04-29T06:01:00+02:00",
            "content": "We are invastigating an increased amount of 500 errors and try to find the cause.",
            "link": "https://github.com/Example/Status/issue/1#issuecomment-123456789"
          }
        ]
      }
    ]
  }
]
```

- `monitor`: Contains information regarding the monitor.
  - `slug`: The slug used for this monitor.
  - `title`: The title for this monitor displayed on the Status page and README file.
  - `url`: The URL that is being monitored.
  - `status`: The current status of this monitor. Can be `up`, `degraded`, `down` or `maintenance`.
  - `uptime`: Contains data regarding the Monitor's uptime. Values depend on the selected range.
    - `percentage`: The percentage of uptime for the monitored range.
    - `response_time`: The response time, in milliseconds, for this monitor.
  - `graph`: Direct URL to the graph displayed in the Status page and README for the displayed range.
- `incidents`: Contains JSON object entries for past and current incidents and planned maintenance schedules
  - `id`: The Issue ID related to this incident.
  - `title`: Title displayed for this incident.
  - `type`: The incident type. Can be `maintenance` or `incident`.
  - `status`: The current status of this incident. Can be `scheduled`, `active` or `completed` for maintenance tasks and `degraded` or `down` for incidents.
  - `times`: ISO formatted Dates and times of when the inciden started and ended (or will start and end).
    - `start`: Date and time of when this incident started. May be a future date for maintenance tasks with status `scheduled`.
    - `end`: Date and time of when this incident ended. May be a future date for maintenance tasks with status `scheduled` or `null` for ongoing incidents.
  - `url`: Link to the related GitHub issue on the repository.
  - `messages`: Array containing entries of posted messages in the issue, sorted from newest to oldest.
    - `author`: Name of the GitHub User who posted the message.
    - `date`: ISO formatted date of when the message was posted.
    - `content`: The message content posted.
    - `link`: Direct message link.
  - `maintenances`: Contains information regarding planned or past maintenance tasks. Not present for incident entries.
    - `expect_down`: Wether to expect the monitored URL to be down.
    - `expect_degraded`: Wether to expect the monitored URL to have degraded performance.

----

#### `/api/monitor/<monitor>?range=<range>`

Returns information of the provided `<monitor>` including past incidents and planned maintenance.

**Path Parameters**  
- `<monitor>`: The monitor to receive information from. Needs to be a valid slug of an existing monitor.

**Query Parameters**  
- `range=<range>` (Optional): Sets the range of which to show data for. Acceptable values are `24h`, `7d`, `30d`, `1y` and `all`. Defaults to `7d` on no/invalid value.

**Response:**

```json
{
  "monitor": {
    "slug": "monitor-1",
    "title": "Monitor 1",
    "url": "https://monitor1.example.com",
    "status": "up",
    "uptime": {
      "percentage": 100.0,
      "response_time": 50
    },
    "graph": "https://raw.githubusercontent.com/Example/Status/master/graphs/monitor-1/response-time-week.png"
  },
  "incidents": [
    {
      "id": 2,
      "title": "Emergency maintenance to fix 500 errors",
      "type": "maintenance",
      "status": "completed"
      "times": {
        "start": "2023-04-30T06:00:00+02:00",
        "end": "2023-04-30T06:25:00+02:00"
      },
      "url": "https://github.com/Example/Status/issue/2",
      "messages": [
        {
          "author": "ExampleUser",
          "date": "2023-04-29T06:15:00+02:00",
          "content": "Maintenance has been completed without any issues.",
          "link": "https://github.com/Example/Status/issue/1#issuecomment-123456713"
        },
        {
          "author": "ExampleUser",
          "date": "2023-04-29T06:01:00+02:00",
          "content": "The emergency maintenance as mentioned in the incident [#1](https://github.com/Example/Status/issue/1) has now started.",
          "link": "https://github.com/Example/Status/issue/1#issuecomment-123456712"
        }
      ],
      "maintenances": {
        "expect_down": true,
        "expect_degraded": false
      }
    },
    {
      "id": 1,
      "title": "Increased amount of 500 errors",
      "type": "incident",
      "status": "down",
      "times": {
        "start": "2023-04-29T06:00:00+02:00",
        "end": "2023-04-29T06:25:00+02:00"
      },
      "url": "https://github.com/Example/Status/issue/1",
      "messages": [
        {
          "author": "ExampleUser",
          "date": "2023-04-29T06:24:00+02:00",
          "content": "A temporary fix has been applied and we will prepare an emergency maintenance to fix this permanently.",
          "link": "https://github.com/Example/Status/issue/1#issuecomment-123456711"
        },
        {
          "author": "ExampleUser",
          "date": "2023-04-29T06:10:00+02:00",
          "content": "We have found the cause of the issue and will apply a temporary fix for it.",
          "link": "https://github.com/Example/Status/issue/1#issuecomment-123456710"
        },
        {
          "author": "ExampleUser",
          "date": "2023-04-29T06:01:00+02:00",
          "content": "We are invastigating an increased amount of 500 errors and try to find the cause.",
          "link": "https://github.com/Example/Status/issue/1#issuecomment-123456789"
        }
      ]
    }
  ]
}
```

- `monitor`: Contains information regarding the monitor.
  - `slug`: The slug used for this monitor.
  - `title`: The title for this monitor displayed on the Status page and README file.
  - `url`: The URL that is being monitored.
  - `status`: The current status of this monitor. Can be `up`, `degraded`, `down` or `maintenance`.
  - `uptime`: Contains data regarding the Monitor's uptime. Values depend on the selected range.
    - `percentage`: The percentage of uptime for the monitored range.
    - `response_time`: The response time, in milliseconds, for this monitor.
  - `graph`: Direct URL to the graph displayed in the Status page and README for the displayed range.
- `incidents`: Contains JSON object entries for past and current incidents and planned maintenance schedules
  - `id`: The Issue ID related to this incident.
  - `title`: Title displayed for this incident.
  - `type`: The incident type. Can be `maintenance` or `incident`.
  - `status`: The current status of this incident. Can be `scheduled`, `active` or `completed` for maintenance tasks and `degraded` or `down` for incidents.
  - `times`: ISO formatted Dates and times of when the inciden started and ended (or will start and end).
    - `start`: Date and time of when this incident started. May be a future date for maintenance tasks with status `scheduled`.
    - `end`: Date and time of when this incident ended. May be a future date for maintenance tasks with status `scheduled` or `null` for ongoing incidents.
  - `url`: Link to the related GitHub issue on the repository.
  - `messages`: Array containing entries of posted messages in the issue, sorted from newest to oldest.
    - `author`: Name of the GitHub User who posted the message.
    - `date`: ISO formatted date of when the message was posted.
    - `content`: The message content posted.
    - `link`: Direct message link.
  - `maintenances`: Contains information regarding planned or past maintenance tasks. Not present for incident entries.
    - `expect_down`: Wether to expect the monitored URL to be down.
    - `expect_degraded`: Wether to expect the monitored URL to have degraded performance.

----

#### `/api/incident?type=<type>&monitor=<monitor>`

Returns past, ongoing and scheduled incidents (Last only for maintenance type incidents), optionally filtered by type and monitor.

**Query Parameters:**  
- `type=<type>` (Optional): The type of incident to only list. Allowed values are `maintenance` and `incident`.
- `monitor=<monitor>` (Optional): The monitor to list incidents for.

**Response:**

```json
[
  {
    "id": 2,
    "title": "Emergency maintenance to fix 500 errors",
    "type": "maintenance",
    "status": "completed"
    "times": {
      "start": "2023-04-30T06:00:00+02:00",
      "end": "2023-04-30T06:25:00+02:00"
    },
    "url": "https://github.com/Example/Status/issue/2",
    "messages": [
      {
        "author": "ExampleUser",
        "date": "2023-04-29T06:15:00+02:00",
        "content": "Maintenance has been completed without any issues.",
        "link": "https://github.com/Example/Status/issue/1#issuecomment-123456713"
      },
      {
        "author": "ExampleUser",
        "date": "2023-04-29T06:01:00+02:00",
        "content": "The emergency maintenance as mentioned in the incident [#1](https://github.com/Example/Status/issue/1) has now started.",
        "link": "https://github.com/Example/Status/issue/1#issuecomment-123456712"
      }
    ],
    "maintenances": {
      "expect_down": true,
      "expect_degraded": false
    }
  },
  {
    "id": 1,
    "title": "Increased amount of 500 errors",
    "type": "incident",
    "status": "down",
    "times": {
      "start": "2023-04-29T06:00:00+02:00",
      "end": "2023-04-29T06:25:00+02:00"
    },
    "url": "https://github.com/Example/Status/issue/1",
    "messages": [
      {
        "author": "ExampleUser",
        "date": "2023-04-29T06:24:00+02:00",
        "content": "A temporary fix has been applied and we will prepare an emergency maintenance to fix this permanently.",
        "link": "https://github.com/Example/Status/issue/1#issuecomment-123456711"
      },
      {
        "author": "ExampleUser",
        "date": "2023-04-29T06:10:00+02:00",
        "content": "We have found the cause of the issue and will apply a temporary fix for it.",
        "link": "https://github.com/Example/Status/issue/1#issuecomment-123456710"
      },
      {
        "author": "ExampleUser",
        "date": "2023-04-29T06:01:00+02:00",
        "content": "We are invastigating an increased amount of 500 errors and try to find the cause.",
        "link": "https://github.com/Example/Status/issue/1#issuecomment-123456789"
      }
    ]
  }
]
```

- `id`: The Issue ID related to this incident.
- `title`: Title displayed for this incident.
- `type`: The incident type. Can be `maintenance` or `incident`.
- `status`: The current status of this incident. Can be `scheduled`, `active` or `completed` for maintenance tasks and `degraded` or `down` for incidents.
- `times`: ISO formatted Dates and times of when the inciden started and ended (or will start and end).
  - `start`: Date and time of when this incident started. May be a future date for maintenance tasks with status `scheduled`.
  - `end`: Date and time of when this incident ended. May be a future date for maintenance tasks with status `scheduled` or `null` for ongoing incidents.
- `url`: Link to the related GitHub issue on the repository.
- `messages`: Array containing entries of posted messages in the issue, sorted from newest to oldest.
  - `author`: Name of the GitHub User who posted the message.
  - `date`: ISO formatted date of when the message was posted.
  - `content`: The message content posted.
  - `link`: Direct message link.
- `maintenances`: Contains information regarding planned or past maintenance tasks. Not present for incident entries.
  - `expect_down`: Wether to expect the monitored URL to be down.
  - `expect_degraded`: Wether to expect the monitored URL to have degraded performance.

#### `/api/incident/<id>`

**Path Parameters:**  
- `<id>`: The incident ID to get the information from. Needs to be a valid (existing) issue ID.

**Response:**

```json
{
  "id": 1,
  "title": "Increased amount of 500 errors",
  "type": "incident",
  "status": "down",
  "times": {
    "start": "2023-04-29T06:00:00+02:00",
    "end": "2023-04-29T06:25:00+02:00"
  },
  "url": "https://github.com/Example/Status/issue/1",
  "messages": [
    {
      "author": "ExampleUser",
      "date": "2023-04-29T06:24:00+02:00",
      "content": "A temporary fix has been applied and we will prepare an emergency maintenance to fix this permanently.",
      "link": "https://github.com/Example/Status/issue/1#issuecomment-123456711"
    },
    {
      "author": "ExampleUser",
      "date": "2023-04-29T06:10:00+02:00",
      "content": "We have found the cause of the issue and will apply a temporary fix for it.",
      "link": "https://github.com/Example/Status/issue/1#issuecomment-123456710"
    },
    {
      "author": "ExampleUser",
      "date": "2023-04-29T06:01:00+02:00",
      "content": "We are invastigating an increased amount of 500 errors and try to find the cause.",
      "link": "https://github.com/Example/Status/issue/1#issuecomment-123456789"
    }
  ]
}
```

- `id`: The Issue ID related to this incident.
- `title`: Title displayed for this incident.
- `type`: The incident type. Can be `maintenance` or `incident`.
- `status`: The current status of this incident. Can be `scheduled`, `active` or `completed` for maintenance tasks and `degraded` or `down` for incidents.
- `times`: ISO formatted Dates and times of when the inciden started and ended (or will start and end).
  - `start`: Date and time of when this incident started. May be a future date for maintenance tasks with status `scheduled`.
  - `end`: Date and time of when this incident ended. May be a future date for maintenance tasks with status `scheduled` or `null` for ongoing incidents.
- `url`: Link to the related GitHub issue on the repository.
- `messages`: Array containing entries of posted messages in the issue, sorted from newest to oldest.
  - `author`: Name of the GitHub User who posted the message.
  - `date`: ISO formatted date of when the message was posted.
  - `content`: The message content posted.
  - `link`: Direct message link.
- `maintenances`: Contains information regarding planned or past maintenance tasks. Not present for incident entries.
  - `expect_down`: Wether to expect the monitored URL to be down.
  - `expect_degraded`: Wether to expect the monitored URL to have degraded performance.

### Response Headers

The following Response Headers should be included in successful, but also failed requests, if a Rate limiting is possible:

| Name               | Value                                                     |
|--------------------|-----------------------------------------------------------|
| Remaining-requests | Number of requests the User can make until the next reset |
| Reset-at           | UNIX timestamp of when the rate limit will be reset       |

For a `429` response should these additional headers be included:

| Name              | Value                                                             |
|-------------------|-------------------------------------------------------------------|
| Wait-before-retry | Time in seconds before the User should do another request         |
| Wait-until        | UNIX timestamp of when the User can perform another request again |

### Failed and invalid Requests

#### Response Body

The response body is a `application/json` one which contains the status code and error message (Reason) as follows:

```json
{
  "code": 429,
  "message": "You've made too many requests. Please wait another 10 seconds before performing any new requests."
}
```

#### Response Codes and messages

The following combination of status code and message should be considered for the following cases:

| Case                     | Code | Message                                                                                     |
|--------------------------|------|---------------------------------------------------------------------------------------------|
| Invalid Monitor slug     | 404  | The provided monitor does not exist.                                                        |
| Invalid Incident ID      | 404  | The provided incident ID does not exist.                                                    |

The following combination of status code and message should also be considered, if technically doable:

| Case                     | Code | Message                                                                                     |
|--------------------------|------|---------------------------------------------------------------------------------------------|
| Making too many requests | 429  | Too many requests have been made. Please wait {n} seconds before doing another request.[^1] |

[^1]: `{n}` would be the number of seconds until a new request could be made. Additionally should `X-` headers be added.

## Limitations and Difficulties

Adding this would require a lot of extra work for the upptime tools to work with. And given that some users may not want an API to be exposed could this also require a configuration option to enable/disable these things.

Understanding the status page right are paths such as `example.com/incident/1` handled on the fly with no actual page in the backend, which most likely could be applied to the API paths too.

The next open question is regarding whether there should be a rate-limiting of sorts in place (if doable using GitHub Pages itself) or not and other things such as caching values for faster and less resource-intensive responses.
