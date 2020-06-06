# Food Review Searching

Create Food Review Searching with **GO** and **Elasticsearch**

## Running

```
docker-compose -f docker-compose.yml up
```

## API Usage

### Get Review By a Specify ID

- Request

    ```
    GET /reviews/<:id>
    ```

- Normal Response

    ```
    {
        "_id": < id >,
        "_index": "reviews",
        "_score": < score >,
        "_source": {
            "created": < Unixtime in Nanosec >,
            "modified": < Unixtime in Nanosec >,
            "reviewid": < reviewID >,
            "reviewtext": < reviewtext >,
        },
        "_type": "_doc",
    }
    ```

- Error Response

    ```
    {
        "message": < error message >,
    }
    ```

### Search Review By Food Text

- Request

    ```
    GET /reviews?query=<:keyword>
    ```

- Normal Response

    ```
    {
        "hits": [
            {
                "_id": < id >,
                "_index": "reviews",
                "_score": < matching score >,
                "_source": {
                    "created": < timeUnix in Nanosec >,
                    "modified": < timeUnix in Nanosec >,
                    "reviewid": < reviewID >,
                    "reviewtext": < reviewtext >,
                },
                "_type": "_doc",
                "highlight": {
                    "reviewtext": [
                        < reviewtext with highlight tag <keyword></keyword> >
                    ],
                }
            },
            { ... < Return maximum 100 documents by rank score > },
        ],
        "max_score": <maximum matching score>,
        "total": {
            "relation": "eq",
            "value": < quantity of all matching documents >
        }
    }
    ```

- Error Response

    ```
    {
        "message": < error message >,
    }
    ```

### Editing Review

- Request

    ```
    PUT /reviews/<:id>

    < raw text in Content-type : text/plain >
    ```

- Normal Response

    ```
    {
        "id": < reviewID >,
        "status": "updated",
    }
    ```

- Error Response

    ```
    {
        "message": < error message >,
        "status": "Not updated",
    }
    ```

## Elasticsearch

Document Input Format

1. Formatting from fest_file.csv

    ```
    type FoodReview struct {
        ID string `json:"reviewid"`
        ReviewText string `json:"reviewtext"`
        CreatedTime int64 `json:"created"`
        ModifiedTime int64 `json:"modified"`
    }
    ```

2. Formatting from food_dictionary.txt

    ```
    type FoodKeyword struct {
        ID string `json:"keywordid"`
        Keyword string `json:"keyword"`
    }
    ```

## Testing

*!!! Please read the function description before the test. It can overwrite the database*

Enable `go test` command and Disable `go build` in in **Dockerfile**

```
...

# RUN go build -o ${APP_NAME}
# CMD ./${APP_NAME}

CMD CGO_ENABLED=0 go test ./...
```

Now, you can test by using this command.

```
docker-compose -f docker-compose.yml up
```

