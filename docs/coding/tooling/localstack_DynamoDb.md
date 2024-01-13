---
title: Localstack and DynamoDb
date: 20240114
author: Dionisio
---

When we are developing, we want to do it locally, so we can do it free of
charge and without worrying about breaking something.

When we are using aws services, localstack is handy. To run it, we can do it
with the following docker compose file:

```yaml
version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME:-localstack-main}"
    image: localstack/localstack:3.0.2
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
    environment:
      # LocalStack configuration: https://docs.localstack.cloud/references/configuration/
      - DEBUG=${DEBUG:-0}
      - START_WEB=1
      - DYNAMODB_SHARE_DB=1
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
```

This docker compose is the one in the localstack documentation, just added the
`DYNAMODB_SHARE_DB=1` which makes the database global, so we don't have a
database per region allowing us to use the region we want in the clients.

With this file, running

```shell
docker compose up -d
```

We have localstack up and running with all services, not only Dynamo.

To connect to the database, we can use [NoSQL Workbench](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.html).

If we don't like it, other clients can be configured (at least datagrip).

We can create a profile with the aws client with:

```shell
aws configure --profile dummy
```

We can fill it with the following values:

```text
AWS Access Key ID [None]: dummy
AWS Secret Access Key [None]: dummy
Default region name [None]: eu-central-1
Default output format [None]:
```

This will create or update the files:

* ~/.aws/config

* ~/.aws/credentials

Adding the profile with passed as parameter.

In datagrip, using:

AWS profile
User: dummy
Profile: dummy
URL: jdbc:dynamodb://localhost:4566?region=eu-west-1

Should connect
