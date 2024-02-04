---
title: Python Wechaty如何使用WorkPro Puppet Service
author: aherman3
tags:
  - python
  - puppet-service
  - workpro
categories: tutorial
image: /assets/2024/02-python-wechaty-for-workpro-puppet-service/python-wechaty-logo.webp
---

Outline how to use WorkPro puppet service with Python, Java, or Go.

- [workpro-getting-started](https://github.com/RPAChat/workpro-getting-started)

[Previous documents](https://wechaty.js.org/docs/puppet-services/padlocal) show how Python, Java, and Go can be used to interface with Wechaty PadLocal puppet service, and these steps can also be applied to WorkPro with some small changes.

Steps

- Contact JuziBOT to purchase a WorkPro token or begin a free 7-day trial token
- Run wechaty-puppet-workpro on a virtual machine
- Verify that the API is successfully registered
- Run local python-wechaty-getting-started (or Java or Go)
- Scan QR code to login

## Step 1: Contact JuziBOT for a token

Contact JuziBOT to purchase a WorkPro token or request a free 7-day trial token.

- [WorkPro Token application](https://wechaty.js.org/docs/puppet-services/workpro)

## Step 2: Run wechaty-puppet-workpro on cloud

You will need a domestic public network IP+Token (please register with the administrator) to make wechaty-puppet take effect in the virtual machine part. For the trial or free plan, you can choose Alibaba Cloud, Huawei Cloud, or GCP.

Register a ubuntu20.04 or above server. In the instance security group, make sure to release a port for public access. This example uses port 8788.

Once your instance is up and running, run the following in the command line.

```shell
apt update
apt install docker.io
docker pull wechaty/wechaty
export WECHATY_PUPPET_SERVICE_TOKEN=puppet_workpro_$YOUR_TOKEN
export WECHATY_PUPPET_SERVER_PORT=8788
export WECHATY_PUPPET=wechaty-puppet-service
export WECHATY_LOG=verbose
export WECHATY_PUPPET_SERVICE_NO_TLS_INSECURE_CLIENT=true
export WECHATY_PUPPET_SERVICE_NO_TLS_INSECURE_SERVER=true
docker run \
  --rm \
  -ti \
  -e WECHATY_LOG \
  -e WECHATY_PUPPET \
  -e WECHATY_PUPPET_SERVICE_TOKEN \
  -e WECHATY_PUPPET_SERVER_PORT \
  -e WECHATY_PUPPET_SERVICE_NO_TLS_INSECURE_CLIENT \
  -e WECHATY_PUPPET_SERVICE_NO_TLS_INSECURE_SERVER \
  -e WECHATY_PUPPET_SERVICE_AUTHORITY \
  -e WECHATY_TOKEN="$WECHATY_PUPPET_SERVICE_TOKEN" \ ## this is your hostie token, so you can also input your own token instead, such as a random UUID
  -p "$WECHATY_PUPPET_SERVER_PORT:$WECHATY_PUPPET_SERVER_PORT" \
  wechaty/wechaty
```

WorkPro has TLS enabled by default, but Wechaty Python does not, so it is necessary to disable TLS for the two to interact.

If you receive a 'token is not found' error, you may need to set the service authority. You can do this by adding this to your environment variable list.

```shell
export WECHATY_PUPPET_SERVICE_AUTHORITY=token-service-discovery-test.juzibot.com 
```

and input the following to your docker command

```shell
-e WECHATY_PUPPET_SERVICE_AUTHORITY
```

## Step 3: Verify hostie token

Visit api.chatie.io/v0/hosties/$WECHATY_TOKEN in your browser to verify that the token is sucessfully verified.

Success: {"ip":"47.119.129.29","port":8788}

Fail: {"ip":"0.0.0.0","port":0}

## Step 4: Run python locally

Clone [python-wechaty-getting-started](https://github.com/wechaty/python-wechaty-getting-started) to your local machine. You can also use [java-wechaty-getting-started](https://github.com/wechaty/java-wechaty-getting-started) or [go-wechaty-getting-started](https://github.com/wechaty/go-wechaty-getting-started).

Run the following commands

```shell
git clone https://github.com/wechaty/python-wechaty-getting-started
cd python-wechaty-getting-started
pip3 install -r requirements.txt
export WECHATY_PUPPET=wechaty-puppet-service
export WECHATY_PUPPET_SERVICE_TOKEN=your_hostie_token_here
make bot
```

A QR code should appear. Scan it with WeChat or WeCom to login.
