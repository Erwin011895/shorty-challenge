Shorty Challenge
================

## The Challenge

The challenge, if you choose to accept it, is to create a micro service to shorten urls, in the style that TinyURL and bit.ly made popular.

## Must-have Requirements

1. The service must expose HTTP endpoints according to the definition below.
1. It must be well tested, it must also be possible to run the entire test suit with a single command from the directory of your repository.

## Nice-to-have Requirements

1. The service must be self contained in a Docker image, but it must be possible to set it up from a fresh install of Ubuntu Server latest LTS, by following the steps you write in the README.
1. You don't have to use a datastore, you can have all data in memory, but you are free if you wanto do use one.
1. The service must be versioned using git and git history **should** be meaningful.

## Tips

* Less is more, small is beautiful, you know the drill — stick to the requirements.
* Use the right tool for the job.
* No need to take care of domains, that's for a reverse proxy to handle.
* Unit tests > Integration tests, but be careful with untested parts of the system.

**Good Luck!** — not that you need any ;)

-------------------------------------------------------------------------

## API Documentation

**All responses must be encoded in JSON and have the appropriate Content-Type header**


### POST /shorten

```
POST /shorten
Content-Type: "application/json"

{
  "url": "https://blog.trello.com/navigate-communication-styles-difficult-times",
  "shortcode": "example"
}
```

Attribute | Description
--------- | -----------
**url**   | url to shorten
shortcode | preferential shortcode

##### Returns:

```
201 Created
Content-Type: "application/json"

{
  "shortcode": :shortcode
}
```

A random shortcode is generated if none is requested, the generated short code has exactly 6 alpahnumeric characters and passes the following regexp: ```^[0-9a-zA-Z_]{6}$```.

##### Errors:

Error | Description
----- | ------------
400   | ```url``` is not present
409   | The the desired shortcode is already in use. **Shortcodes are case-sensitive**.
422   | The shortcode fails to meet the following regexp: ```^[0-9a-zA-Z_]{6}$```.


### GET /:shortcode

```
GET /:shortcode
Content-Type: "application/json"
```

Attribute      | Description
-------------- | -----------
**shortcode**  | url encoded shortcode

##### Returns

**302** response with the location header pointing to the shortened URL

```
HTTP/1.1 302 Found
Location: https://blog.trello.com/navigate-communication-styles-difficult-times
```

##### Errors

Error | Description
----- | ------------
404   | The ```shortcode``` cannot be found in the system

### GET /:shortcode/stats

```
GET /:shortcode/stats
Content-Type: "application/json"
```

Attribute      | Description
-------------- | -----------
**shortcode**  | url encoded shortcode

##### Returns

```
200 OK
Content-Type: "application/json"

{
  "startDate": "2012-04-23T18:25:43.511Z",
  "lastSeenDate": "2012-04-23T18:25:43.511Z",
  "redirectCount": 1
}
```

Attribute         | Description
--------------    | -----------
**startDate**     | date when the url was encoded, conformant to [ISO8601](http://en.wikipedia.org/wiki/ISO_8601)
**redirectCount** | number of times the endpoint ```GET /shortcode``` was called
lastSeenDate      | date of the last time the a redirect was issued, not present if ```redirectCount == 0```

##### Errors

Error | Description
----- | ------------
404   | The ```shortcode``` cannot be found in the system