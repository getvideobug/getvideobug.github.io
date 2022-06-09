---
title: "Deploying on GCP with chef"
date: 2022-05-12T12:58:07+05:30
draft: true
---



## Using Google Deployment Manager (~5 mins)


### Service account HMAC key generation


1. create a new service account
2. with perission roles/storage.objectAdmin (access to google cloud storage)
3. generate HMAC keys

```bash

gcloud iam service-accounts create videobug-service-account --display-name "videobug service account"
gcloud projects add-iam-policy-binding $(gcloud config get-value project)  --member="serviceAccount:videobug-service-account@$(gcloud config get-value project).iam.gserviceaccount.com"  --role="roles/storage.objectAdmin"


gsutil hmac create videobug-service-account@$(gcloud config get-value project).iam.gserviceaccount.com

Access ID:   GOOG1EK3UCPL6K6OJB6LNGM7DSFU4U22SPVGHQTIV4KXXXXXXXXXXXXXXXXXX
Secret:      XXX/OSxxQ0xxxxJ0WxRTxx9YFx5xx0AxF1SPxxxx

```

Set your project as default in google storage interopability (open in new tab and check under heading "Default project for interoperable access")

https://console.cloud.google.com/storage/settings;tab=interoperability?project=<project>

### Deployment

Download videobug.jinja

```bash
wget https://videobug-dev.s3.ap-south-1.amazonaws.com/videobug.jinja
```

Execute

```bash
gcloud deployment-manager deployments create videobug --template=videobug.jinja --properties="zone:'us-west1-a',region:'us-west1',accessKey:'<ACCESS-KEY>',secretKey:'<SECRET-KEY>'"
```

Get Public IP

```bash
gcloud compute addresses describe  --global videobug-public-ip

address: 35.111.45.162
addressType: EXTERNAL
creationTimestamp: '2022-05-31T23:44:53.432-07:00'
description: ''
id: '5991168970906579978'
kind: compute#address
name: videobug-public-ip
networkTier: PREMIUM
selfLink: https://www.googleapis.com/compute/v1/projects/shining-camp-344911/global/addresses/videobug-public-ip
status: IN_USE
users:
- https://www.googleapis.com/compute/v1/projects/shining-camp-344911/global/forwardingRules/videobug-http
```


### SNAP commands to modify configuration

sudo snap set videobug-server "s3-endpoint=https://storage.googleapis.com"
sudo snap set videobug-server "s3-bucketname=videobug-state"
sudo snap set videobug-server "s3-access-key=<KEY>"
sudo snap set videobug-server "s3-secret-key=<SECRET>"
sudo snap set videobug-server "aerospike-namespace=videobug"
sudo snap set videobug-server "aerospike-hosts=10.182.0.36:3000"



