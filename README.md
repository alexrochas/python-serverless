# Python Serverless

## Create lambda
```
awslocal lambda create-function --role hello-role --function-name hello --runtime python3.6 --handler handler.hello --zip-file fileb://package.zip
```

## Create Rest API
```
awslocal apigateway create-rest-api --region us-east-1 --name 'API Test'
```

```
# return
{
    "name": "API Test",
    "createdDate": 1540053994,
    "id": "6820211674"
}
```

```
# parent-id
awslocal apigateway get-resources --region us-east-1 --rest-api-id 682021167
```

```
# return
{
    "items": [
        {
            "id": "A-Z78901A-Z599",
            "resourceMethods": {
                "GET": {}
            },
            "path": "/"
        }
    ]
}
```

## Create resource
```
awslocal apigateway create-resource \
--region us-east-1 \
--rest-api-id 6820211674 \
--parent-id A-Z78901A-Z599 \
--path-part "hello"
```

```
# return
{
    "path": "/hello",
    "id": "2755323557",
    "parentId": "A-Z78901A-Z599",
    "resourceMethods": {
        "GET": {}
    },
    "pathPart": "hello"
}
```

## Create linked GET method
```
awslocal apigateway put-method \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --resource-id 2755323557 \
 --http-method GET \
 --authorization-type "NONE"
```

## List functions
```
awslocal lambda list-fucntions
```

```
# return
{
    "Functions": [
        {
            "FunctionName": "hello",
            "Version": "$LATEST",
            "FunctionArn": "arn:aws:lambda:us-east-1:000000000000:function:hello",
            "Runtime": "python3.6",
            "Environment": {},
            "CodeSize": 50,
            "Handler": "handler.hello"
        }
    ]
}
```

## Create integration
```
awslocal apigateway put-integration \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --resource-id 2755323557 \
 --http-method GET \
 --type AWS_PROXY \
 --integration-http-method GET \
 --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:000000000000:function:hello/invocations \
 --passthrough-behavior when_no_match
```

```
# result
{
    "httpMethod": "GET",
    "integrationResponses": {
        "200": {
            "statusCode": 200,
            "responseTemplates": {
                "application/json": null
            }
        }
    },
    "type": "aws_proxy",
    "uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:000000000000:function:hello/invocations"
}
```

## Deploy API
```
awslocal apigateway create-deployment \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --stage-name test
```

```
# response
{
    "description": "",
    "id": "0A-Z760348A-Z8",
    "createdDate": 1540056138
}
```

## Test it!
```
curl http://localhost:4567/restapis/6820211674/test/_user_request_/hello
```
