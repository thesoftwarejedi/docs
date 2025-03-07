---
title: TimescaleDB with AWS Lambda
excerpt: Learn to use AWS Lambda and TimescaleDB together
keywords: [finance, analytics, AWS Lambda, psycopg2, pandas, GitHub Actions, pipeline]
---

# TimescaleDB with AWS Lambda

This section contains tutorials for working with AWS Lambda and TimescaleDB.

*   [Create a data API for TimescaleDB][create-data-api] using AWS Lambda and
    API Gateway.
*   [Pull data from 3rd party API and ingest into TimescaleDB][3rd-party-ingest]
    using AWS Lambda and Docker. This is great if you have a lot of dependencies.
*   [Continuously deploy your Lambda function with GitHub Actions][gh-actions]
    using Github Actions.

## Prerequisites

Before you begin, make sure you have completed the
[Analyze intraday stock data tutorial](https://docs.timescale.com/timescaledb/latest/tutorials/analyze-intraday-stocks/).
This tutorial needs the tables and data that you set up in that tutorial.

To complete this tutorial, you need an AWS account. You also need access to the
AWS command-line interface (CLI). To check if you have AWS CLI installed, use
this command at the command prompt. If it is installed, the command shows the
version number, like this:

```bash
aws --version
aws-cli/2.2.18 Python/3.8.8 Linux/5.10.0-1044-oem exe/x86_64.ubuntu.20 prompt/off
```

For more information about installing the AWS CLI, see
[the AWS installation instructions][aws-install].

<Highlight type="cloud" header="VPC on Timescale Cloud" button="Try for free">
If you are completing this tutorial in Timescale Cloud, make sure you have
created a VPC on both AWS, and on your database in Timescale Cloud. For more
information about setting up a VPC, see the
[Timescale Cloud VPC section](/cloud/latest/service-operations/vpc/).
</Highlight>

## Programming language

The code examples in this tutorial use Python, but you can use any language
[supported by AWS Lambda][lambda-supported-langs].

## Resources

For more information about the topics in this tutorial, check out these resources:

*   [AWS CLI Version 2 References][aws-cli2]
*   [Creating Lambda container images][lambda-container-images]
*   [Getting started with AWS Lambda][lambda-getting-started]
*   [Analyze historical intraday stock data][intraday-stock-data]
*   [Analyze cryptocurrency market data][cryptocurrency-market-data]

[3rd-party-ingest]: /timescaledb/:currentVersion:/tutorials/aws-lambda/3rd-party-api-ingest
[aws-cli2]: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/index.html
[aws-install]: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
[create-data-api]: /timescaledb/:currentVersion:/tutorials/aws-lambda/create-data-api
[cryptocurrency-market-data]: /timescaledb/:currentVersion:/tutorials/analyze-cryptocurrency-data
[gh-actions]: /timescaledb/:currentVersion:/tutorials/aws-lambda/continuous-deployment
[intraday-stock-data]: /timescaledb/:currentVersion:/tutorials/analyze-intraday-stocks
[lambda-container-images]: https://docs.aws.amazon.com/lambda/latest/dg/images-create.html
[lambda-getting-started]: https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html
[lambda-supported-langs]: https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html
