# Using the Loomio API

Interested in interacting with Loomio from outside of Loomio? Awesome, you're in the right place!

This is intended to be a comprehensive reference of what's possible with the Loomio API. It's very much a work in progress at the moment.

NB: All of the curl commands in this document have been shortened for readability. IE, a curl command like this:

```bash
  curl /discussions.json
```

actually, of course, means this:
```bash
  curl http://www.loomio.org/api/v1/discussions.json
```

Happy cUrling and bashing!

## Using the Public API

Data in public discussions is freely available via the Loomio API. Certain endpoints (marked with a 🔓 in the [API Endpoints](#api_endpoints) section below) are accessible even while logged out. For example, you can view a list of the most recently created public discussions on (Loomio.org)[http://www.loomio.org] like so:

```bash
  curl /discussions.json
```

## Getting an OAuth application set up

Need access to private user data? You'll need to set up an OAuth application to authenticate yourself.
NB: This workflow is a work in progress at the moment. Coming soon!

## API Response Information

#### Side loading

Responses from the Loomio API all utilize a strategy known as [side loading](https://www.netguru.co/blog/active-model-serializers-ember) (also known as 'compound document')

This means that instead of loading all records in a nested structure, like this:

```json
# NB: Not what actually happens:
# GET api/v1/discussions/1
{
  "id": 1,
  "title": "My awesome discussion",
  "group": {
    "id": 2,
    "name": "My awesome group"
  }
}
```

we load all of the records in the response in a flat structure, side by side, like so:
```json
{
  "discussions": [
    {
      "id": 1,
      "title": "My awesome discussion",
      "group_id": 2
    }
  ],
  "groups": [
    {
      "id": 2,
      "name": "My awesome group"
    }
  ]
}
```

This makes it really easily to import records into a clientside record store (which is what we do with them), but can mean it's a little trickier to extract a particular record from a response.

```ruby
# getting a single discussion
response = RestClient.get('api/v1/discussions/1.json')
console.log response['discussions'][0]
# => { id: 1, title: "My awesome discussion", group_id: 2 }
console.log response['groups'][0]
# => { id: 2, name: "My awesome group" }
```

```ruby
# getting a list of discussions
response = RestClient.get('api/v1/discussions.json')
console.log response['discussions']
# => [
#     { id: 1, title: "My awesome discussion", group_id: 2 },
#     { id: 2, title: "My other discussion", group_id: 3 }
#    ]

console.log response['groups']
# => [
#     { id: 2, name: "My awesome group" },
#     { id: 3, name: "My other group" }
#    ]
```

#### Error handling

When something goes wrong, we'll do our best to let you know why.
All errors will come back with an appropriate response code
(accessible via the `status` field), and an exception message:

```ruby
  response = RestClient.get('api/v1/discussions/not_found.json')
  console.log response['exception'] if response.status == 404 # => "Resource not found"
```

Possible errors include:

**400 - Bad request**
The request was malformed or missing required values; check your parameters and try again.

**403 - Unauthorized**
You do not have permission to perform this action with the supplied credentials.
Check that your application has the correct permissions and try again.

**404 - Resource Not Found**
The requested resource could not be found; it may have been deleted or moved.

**418 - I'm a Teapot**
Something has gone horribly wrong and you have [confused Loomio with a teapot](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#418).

**422 - Unprocessable Entity**
The form values submitted with this request could not be properly processed
(ie, they were missing some required fields, or there were other validations errors)
Try checking the form and resubmitting.

**429 - Too many requests**
You (or someone like you) has been hitting the API a _little_ too hard recently. Take a break and try again later. (TODO: implement and document a simple rate limiting system for the API)

**500 - Internal Server Error**
Something went wrong on our end, whoops! We'd really appreciate you letting us know
by sending an email to [contact@loomio.org](mailto:contact@loomio.org) or [filing an issue](http://github.com/loomio/loomio/issues)

## API Endpoints

#### General Knowledge

There are two main types of endpoint -- ones which return a single record, and ones which return a collection of records, each with their own standardized sets of parameters.

###### Individual record parameters
`show`, `update`, and `destroy`, as well as other member endpoints, *require* an id parameter to specify which record is operated in. This can be either in the format of the record id, or the record key.

```bash
  # GET a discussion by id
  curl /discussions/1.json
```
or,
```bash
  # UPDATE a discussion by key
  curl -x PUT --data "title":"Let's go to the moon!" /discussions/UlXZMHwE.json
```

###### Collection record parameters
`index` and other collection endpoints accept a series of parameters for specifying the timeframe and pagingation of the records which are returned. They're outlined below:

**from**: The number of records to skip in the collection returned (default is 0)
```bash
  # GET all discussions except for the first 10
  curl "/discussions.json?from=10"
```

**per**: The number of records to return in the collection (default is 50)
```bash
  # GET the first 10 discussions
  curl "/discussions.json?per=10"
```

**since**: Filter records which have occurred after the given date (defaults to long, long ago)
```bash
  # GET all discussions created in 2016
  curl "/discussions.json?since=Fri Jan 1 2016"
```

**until**: Filter records which have occurred before the given date (defaults to way in the future)
```bash
  # GET all discussions created before 2016
  curl "/discussions.json?until=Fri Jan 1 2016"
```

**timeframe_for**: Specify which field to apply the previous timeframe values to. Accepts any datetime field (the ones ending in `_at`), defaults to `created_at`
```bash
  # GET all discussions with activity in 2016
  curl "/discussions.json?timeframe_for=last_activity_at&since=Fri Jan 1 2016"
```

These can also be combined:
```bash
  # GET records 10 - 20 which have been updated in 2011
  curl "/discussions.json?from=10per=10&timeframe_for=updated_at&since=Thu Jan 1 2011&until=Fri Jan 1 2012"
```

These options are further documented in the [README](http://github.com/loomio/snorlax) for the snorlax gem which Loomio uses to serve API requests

#### Discussions

**Example JSON for a discussion**:
```json
{
  "id":                       "The discussion's id (integer)",
  "key":                      "The discussion's key (string)",
  "title":                    "The discussion's title (string)",
  "description":              "The discussion's description (string)",

  "private":                  "Whether the discussion is marked as private (boolean)",
  "author_id":                "The id of the discussion author (integer, side loaded record)",
  "group_id":                 "The id of the group the discussion belongs to (integer, side loaded record)",
  "active_proposal_id":       "The id of the current active proposal in the discussion (integer, side loaded record, can be null)",

  "items_count":              "The number of events associated with this discussion (integer)",
  "salient_items_count":      "The number of salient events associated with this discussion (integer)",
  "comments_count":           "The number of comments in the discussion (integer)",
  "first_sequence_id":        "The sequence_id of the first event in the discussion (integer)",
  "last_sequence_id":         "The sequence_id of the last event in the discussion (integer)",

  "last_item_at":             "The time the last event was created (datetime, can be null)",
  "last_comment_at":          "The time the last new_comment event was created (datetime, can be null)",
  "last_activity_at":         "The last time there was event activity in the thread (datetime, can be null)",
  "created_at":               "The time the discussion was created (datetime)",
  "updated_at":               "The time the discussion was updated (datetime, can be null)",
  "archived_at":              "The time the discussion was archived (datetime, can be null)",

  "discussion_reader_id":     "The id of the reader for this discussion and the logged in user (integer, can be null)",
  "discussion_reader_volume": "The volume of the discussion to the current user (string, can be null)",
  "participating":            "Whether the current user is participating in the discussion (boolean, can be null)",
  "starred":                 "Whether the current user has starred the discussion (boolean, can be null)",

  "read_items_count":         "How many events the current user has read (integer, can be null)",
  "read_comments_count":      "The number of comments the current user has read (integer, can be null)",
  "read_salient_items_count": "How many salient events the current user has read (integer, can be null)",
  "last_read_sequence_id":    "The sequence_id of the last read event (integer)",
  "last_read_at":             "The last time the current user read this discussion (datetime, can be null)",
}
```

**A note about salient items**:

**A note about discussion readers**:

**Index**
Returns a list of discussions, which can be constrained by a group id

Parameters: regular [collection endpoint parameters](somewhere)

example:

```ruby
response = RestClient.get('loomio.org/api/v1/discussions.json')
```
