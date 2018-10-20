# Python Serverless
> Minor POC to test AWS Lambda over rest-api

## How-to

### Zip it
```bash
~/path/to/project$ zip package.zip handler.py
```

### Create lambda
```bash
~/path/to/project$ awslocal lambda create-function --role hello-role --function-name hello --runtime python3.6 --handler handler.hello --zip-file fileb://package.zip
```

### Create Rest API
```bash
~/path/to/project$ awslocal apigateway create-rest-api --region us-east-1 --name 'API Test'
```

will return:
```json
{
    "name": "API Test",
    "createdDate": 1540053994,
    "id": "6820211674"
}
```

```bash
# parent-id
~/path/to/project$ awslocal apigateway get-resources --region us-east-1 --rest-api-id 682021167
```

will return:
```json
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

### Create resource
```bash
~/path/to/project$ awslocal apigateway create-resource \
--region us-east-1 \
--rest-api-id 6820211674 \
--parent-id A-Z78901A-Z599 \
--path-part "hello"
```

will return:
```json
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

### Create linked GET method
```bash
~/path/to/project$ awslocal apigateway put-method \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --resource-id 2755323557 \
 --http-method GET \
 --authorization-type "NONE"
```

### List functions
```bash
~/path/to/project$ awslocal lambda list-fucntions
```

will return:
```json
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

### Create integration
```bash
~/path/to/project$ awslocal apigateway put-integration \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --resource-id 2755323557 \
 --http-method GET \
 --type AWS_PROXY \
 --integration-http-method GET \
 --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:000000000000:function:hello/invocations \
 --passthrough-behavior when_no_match
```

will return:
```json
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

### Deploy API
```bash
~/path/to/project$ awslocal apigateway create-deployment \
 --region us-east-1 \
 --rest-api-id 6820211674 \
 --stage-name test
```

will return:
```json
{
    "description": "",
    "id": "0A-Z760348A-Z8",
    "createdDate": 1540056138
}
```

### Test it!
```bash
~/path/to/project$ curl http://localhost:4567/restapis/6820211674/test/_user_request_/hello
```

will return:
```json
{  
   "input":{  
      "body":null,
      "headers":{  
         "host":"localhost:4567",
         "accept":"*/*",
         "user-agent":"curl/7.47.0"
      },
      "resource":"/restapis/6820211674/hlg/_user_request_/hello",
      "queryStringParameters":{  

      },
      "httpMethod":"GET",
      "stageVariables":{  

      },
      "path":"/hello",
      "pathParameters":{  

      },
      "isBase64Encoded":false
   },
   "message":"Go Serverless v1.0! Your function executed successfully!"
}
```

## Roadmap
* implement cloudformation

## Meta

Alex Rocha - [about.me](http://about.me/alex.rochas)
