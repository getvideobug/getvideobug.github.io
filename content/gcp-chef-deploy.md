---
title: "Deploying on GCP with chef"
date: 2022-05-12T12:58:07+05:30
draft: true
---


```bash
gcloud iam service-accounts create chef-service-account --display-name "chef service account"

gcloud iam service-accounts keys create chef-account-key.json  --iam-account chef-service-account@shining-camp-344911.iam.gserviceaccount.com
```




