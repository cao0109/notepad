# ElasticMQ本地模拟使用AWS的SQS

查询队列

```powershell
aws sqs list-queues --endpoint-url http://localhost:9324
```

 创建队列

```powershell
aws sqs create-queue --queue-name test-queue --endpoint-url 

http://localhost:9324
{
    "QueueUrl": "http://localhost:9324/queue/test-queue"
}
```

 向Q中发送消息 

```powershell
aws sqs send-message --queue-url http://localhost:9324/queue/test-queue --message-body "Information about the largest city in Any Region." --endpoint-url http://localhost:9324

{
    "MD5OfMessageBody": "51b0a3256d59467f973009b739163aa0",
    "MD5OfMessageAttributes": "d41d8cd98f00b204e9800998ecf8427e",
    "MessageId": "2f2af1fc-dbf7-40a6-8783-756181eeb737"
}
```

 从Q中接受消息 

```powershell
aws sqs receive-message --queue-url http://localhost:9324/queue/test-queue --endpoint-url http://localhost:9324
{
    "Messages": [
        {
            "MessageId": "2f2af1fc-dbf7-40a6-8783-756181eeb737",
            "ReceiptHandle": "2f2af1fc-dbf7-40a6-8783-756181eeb737#0893169f-63c7-47ef-bde2-5ad26fad7e1d",
            "MD5OfBody": "51b0a3256d59467f973009b739163aa0",
            "Body": "Information about the largest city in Any Region.",
            "MD5OfMessageAttributes": "d41d8cd98f00b204e9800998ecf8427e"
        }
    ]
}
```



# nodejs 中操作ElasticMQ

```js
var AWS = require('aws-sdk\\global');
var SQS = require('aws-sdk\\clients\\SQS');


var myCredentials = new AWS.Credentials("x", "x");

var sqs = new AWS.SQS({
    apiVersion: '2012-11-05', 
    credentials: myCredentials,
    region: "none",
    endpoint: "http://localhost:9324"
});

var params = {};

//sample code from amazon
console.log("calling listQueues");
//call for SQS list
sqs.listQueues(params, function (err, data) {
    if (err) {
        console.log("Error", err);
    } else {
        console.log("Success", data.QueueUrls);
    }
});
```

# 消费端

```js
npm i sqs-consumer --save

const { Consumer } = require("sqs-consumer");
```

