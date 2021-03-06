# PullLogs {#reference_f5p_ytq_12b .reference}

Obtain logs based on the cursor and quantity.  You must specify a shard when the system obtains logs.  In scenarios such as Storm, elective and collaborative consumption can be performed by using [LoghubClientLib](../../../../intl.en-US/User Guide/Real-time subscription and consumption/Consumer group - Usage.md) . Currently, only log group list in [Protocol Buffer \(PB\) ](intl.en-US/API Reference/Common resources/Data encoding method.md) format can be read.If storm can pass through storm and so on.

## Request syntax {#section_j5v_14t_12b .section}

```
GET /logstores/ay42/shards/0? type=logs&cursor=MTQ0NzMyOTQwMTEwMjEzMDkwNA==&count=100 HTTP/1.1
Accept: application/x-protobuf
Accept-Encoding: lz4
Authorization: <AuthorizationString>
Date: <GMT Date>
Host: <Project Endpoint>
x-log-apiversion: 0.6.0
x-log-signaturemethod: hmac-sha1
```

## Request parameters {#section_mx5_b4t_12b .section}

URL parameters:

|Parameter name|Type |Required|Description |
|:-------------|:----|:-------|:-----------|
|type |string |Yes |The logs.|
|cursor|string|Yes|A cursor used to indicate where to start reading data, which is equivalent to the start point.|
|count|int |Yes|The number of returned log groups, ranging from 0 to 1000.|

**Logstore lifecycle:**

The lifecycle of a logstore is specified by the lifeCycle field in the attribute. For example, the current time is 2015-11-11 At 09:00:00, lifecycle = 24. Therefore, the consumable data time section in each shard is \[2015-11-10 09:00:00, 2015-11-11 09:00:00\]. The time here is the server time. Server Side time.

From allows you to locate logs in the life cycle in Shard, assuming that the logstore life cycle is \[Begin\_time, end\_time\), from = from\_time

```
 From_time <v= begin_time or from_time = "begin ": returns a cursor location with a point-in-time of begin_time
 From_time< = end_time or from_time = "end": Return to the current point in time, the next cursor location to be written (currently there is no data on the cursor location)
 From_time< begin_time and from_time <end_time: returns the cursor corresponding to the packet for the first server receive time> = from_time
```

**Request header**

-   Accept: application/x-protobuf
-   Accept-Encoding: lz4, deflate, or “”

For more information about the public request headers of Log Service APIs, see [Public request header](intl.en-US/API Reference/Public request header.md).

**Response header**

-   x-log-cursor: The next cursor of the currently read data.
-   x-log-count: The number of currently returned logs.

For more information about the public response headers of Log Service APIs, see [Public response header](intl.en-US/API Reference/Public response header.md).

**Response element**

Serialized data \(which may be compressed\) in PB format.

**Error code**

Besides  the [common error codes](intl.en-US/API Reference/Common error codes.md) of Log Service APIs, the PullLogs API may return the following special error codes.

|HTTP status code |Error Code|Error Message|
|:----------------|:---------|:------------|
|404|LogStoreNotExist|Logstore \{Name\} does not exist|
|400|Parameterinvalid|Parameter Cursor is not valid|
|400|ParameterInvalid|ParameterCount should be in \[0-1000\]|
|400|Shardnotexist|Shard \{ShardID\} does not exist|
|400|InvalidCursor|this cursor is invalid|
|500|InternalServerError|Specified Server Error Message|

## Example {#section_e5p_d4t_12b .section}

**Request example:**

```
Read data from shard 0.
GET /logstores/sls-test-logstore/shards/0? cursor=MTQ0NzMyOTQwMTEwMjEzMDkwNA==&count=1000&type=log  
Header:
{
    "Authorization"="LOG 94to3z418yupi6ikawqqd370:WeMYZp6bH/SmWEgryMrLhbxK+7o=", 
    "x-log-bodyrawsize"=0, 
    "User-Agent" : "sls-java-sdk-v-0.6.0", 
    "x-log-apiversion" : "0.6.0", 
    "Host" : "ali-test-project.cn-hangzhou-failover-intranet.sls.aliyuncs.com", 
    "x-log-signaturemethod" : "hmac-sha1", 
    "Accept-Encoding" : "lz4", 
    "Content-Length": 0,
    "Date" : "Thu, 12 Nov 2015 12:03:17 GMT",
    "Content-Type" : "application/x-protobuf", 
    "accept" : "application/x-protobuf"
}
```

**响应示例：**

```
Header:
{
    "x-log-count" : "1000", 
    "x-log-requestid" : "56447FB20351626D7C000874", 
    "Server" : "nginx/1.6.1", 
    "x-log-bodyrawsize" : "34121", 
    "Connection" : "close", 
    "Content-Length" : "4231", 
    "x-log-cursor" : "MTQ0NzMyOTQwMTEwMjEzMDkwNA==", 
    "Date" : "Thu, 12 Nov 2015 12:01:54 GMT", 
    "x-log-compresstype" : "lz4", 
    "Content-Type" : "application/x-protobuf"
}
Body:
The <log group list in PB format> after the compression.
```

**Page flip**

To flip the page \(get the next  token\) without returning data, the system can send HTTP HEAD requests.

