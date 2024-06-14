
<!-- toc is generated with GitHub Actions do not remove toc markers -->
# AsyncAPIQuantum template
<!-- toc -->

- [Overview](#overview)
- [Technical requirements](#technical-requirements)
- [Specification requirements](#specification-requirements)
- [Supported protocols](#supported-protocols)
- [How to use the template](#how-to-use-the-template)
  * [Extensions](#extensions)
- [Template configuration](#template-configuration)
- [Development](#development)

<!-- tocstop -->

## Overview
This template is based on the AsyncAPI NodeJS template with additional extensions that provide the use of quantum code on Amazon Braket and obtaining the results of the quantum machine asynchronously, see [Extensions](#extensions) for more information.

This template generates a Node.js application with any of the [supported protocols](#supported-protocols) endpoint, based on [Hermesjs](https://github.com/hitchhq/hermes).

Other files are for the setup of developer environment, like `.editorconfig` or `.eslint`.

This template also can execute quantum software on Amazon Braket using the extensions provided.

## Technical requirements

- 0.50.0 =< [Generator](https://github.com/asyncapi/generator/) < 2.0.0,
- Generator specific [requirements](https://github.com/asyncapi/generator/#requirements)


## Specification requirements

Property name | Reason | Fallback | Default
---|---|---|---
`operationId` | Operation ID must be set for every operation to generate proper functions as there is no fallback in place | - | -


## Supported protocols

* [AMQP](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)
* [MQTT and MQTTS](https://en.wikipedia.org/wiki/MQTT)
* [Kafka](https://en.wikipedia.org/wiki/Apache_Kafka)
* [WebSocket](https://en.wikipedia.org/wiki/WebSocket)

## How to use the template

This template must be used with the AsyncAPI Generator. You can find all available options [here](https://github.com/asyncapi/generator/).

In case you use X509 security and need to provide certificates, either place them in the root of the generated server with the following names: `ca.pem`, `service.cert`, `service.key`. You can provide a custom directory where cert files are located using `certFilesDir` parameter like `-p certFilesDir=../not/in/my/app/dir`.

Since you can have multiple different security schemes, to use the one of X509 type, you need to pass the name of the scheme like this: `-p securityScheme=SCHEME_NAME`.

> You can find a complete tutorial on AsyncAPI Generator using this template [here](https://www.asyncapi.com/docs/tutorials/streetlights). 

### Extensions

This template contains two extensions, x-quantum and x-quantum-awslambda. The extensions are optional and the developer can run this template without using them. For now, this template only works with AWS.

x-quantum extension use is to reference a URL with a RAW format file that should contain the quantum code that will be executed when the API sends or recieves a message thorugh a specified channel. This extension also can have a machine parameter indicating the quantum computer that will run the code. If you use this parameter, your Python file should use CLI arguments and the machine will be the first of the arguments (sys.argv[1]).

x-quantum-awslambda extension generates the Lambda function to recieve the result of the quantum computer asynchronously. You can specify the channel in which the Lambda function will send the data. It is important to note that for now this extension is only compatible with MQTT and MQTTS protocols.


In the following table, section indicates the section of the AsyncAPI document where the extension must be placed.

Extension | Section | Description
----------|-----------|-------------
x-quantum | operation | This extension is an object that requires two other fields: quantum_url and quantum_machine.
x-quantum-awslambda | asyncapi | This extension is an object that requires four fields: channel, s3_bucket, role_arn and region. For now, the code generated by this extension is only compatible with MQTT and MQTTS servers.


The following table indicates the parameters of each extension. Parameter is the name of the parameter of the extension and extension indicates in which of the previous two extension the parameter must be placed.

Parameter | Extension | Description
----------|-----------|-------------
quantum_url | x-quantum | This parameter indicates the URL of the quantum code that is going to run when the API recieves/sends a message thorugh the channel.
quantum_machine | x-quantum | This parameter indicates the Amazon Braket machine in which the code will execute. This parameter is optional.
channel | x-quantum-awslambda | This parameter indicates the channel in which the Lambda function is going to send the result of the quantum computer.
s3_bucket | x-quantum-awslambda | This parameter indicates the bucket that is going to trigger the Lambda function.
role_arn | x-quantum-awslambda | This parameter indicates the AWS IAM role arn that will execute the Lambda function.
region | x-quantum-awslambda | This parameter indicates the region of the S3 bucket.

There is an example at https://github.com/jorgecs/AsyncAPISample/blob/main/example.yaml

## Template configuration

You can configure this template by passing different parameters in the Generator CLI: `-p PARAM1_NAME=PARAM1_VALUE -p PARAM2_NAME=PARAM2_VALUE`

|Name|Description|Required|Example|
|---|---|---|---|
|server|The server you want to use in the code.|Yes|`production`|
|securityScheme|Name of the security scheme. Only scheme with X509 and Kafka protocol is supported for now.|No|'mySchemeName'|
|certFilesDir|Directory where application certificates are located. This parameter is needed when you use X509 security scheme and your cert files are not located in the root of your application.|No|`../not/in/my/app/dir`|

## Development

To use this template, download it or clone with

```bash
$ git clone https://github.com/jorgecs/AsyncAPIQuantum.git
```
Then, generate the code with
```bash
$ ag ~/AsyncApiDocument.yaml ./AsyncAPIQuantum -o output -p server=production
```
