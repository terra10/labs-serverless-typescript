# TERRA 10 Serverless labs

## Lab 007 - Get just 1 beer (why?)

## Path Parameters
We now have, through the completion of [Lab006](../Lab006), the identification of all of our beers, so let's use path parameters to just get 1 beer al 'all' is a little too much sometimes. ;-)

Create a `src/getBeer.ts` and use below example code:

```
    try {
        const identification = event.pathParameters.identifier;

        const queryParams = {
            TableName: 't10a-serverless',
            Key: {identification}
        };

        const result = await documentClient.get(queryParams).promise();
        if (result.Item) {
            return {
                statusCode: 200,
                headers,
                body: JSON.stringify(result.Item)
            };
        }
        console.error('Return 404 | Geen item gevonden');
        return {
            statusCode: 404,
            headers,
            body: undefined
        };
```

Run `tsc` and make sure to add the function to serverless.yml:
```
  getBeer:
    handler: lib/getBeer.handler
    description: GET one beer
    events:
      - http:
          method: get
          path: beer/{identifier}
          private: true
```

Make sure you understand the path parameter mentioned here and how it is exposed from the API Gateway event in the code. You should also check the API Gateway in the AWS Console.

## IAM
Remember to add this to the IAM role segment in serverless.yml where we now also need dynamodb:GetItem.

## Test
Compile and deploy (`tsc && serverless deploy`). Then use Postman to execute a GET on /beer/xxxxx to get 1 of the beers from DynamoDB.

## Solution
If you want check the /solution/getBeer.ts.