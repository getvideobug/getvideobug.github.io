---
title: "Deploying on GCP with chef"
date: 2022-05-12T12:58:07+05:30
draft: true
---

## Create a new project

```shell
export TF_VAR_PROJECT="videobug"
gcloud projects create $TF_VAR_PROJECT
gcloud projects list
```

### Create IAM Account

And obtain the credentials JSON file

Role: Project Editor

```shell
gcloud iam service-accounts create videobug-service-account --display-name "videobug service account"

gcloud iam service-accounts list

gcloud iam service-accounts keys create credentials.json  --iam-account videobug-service-account@<PROJECT_ID>.iam.gserviceaccount.com
```

Create HMAC access-key and secret-key

```shell
ls -lah credentials.json

gsutil hmac create videobug-service-account@<PROJECT_ID>.iam.gserviceaccount.com

```

Create a GCS bucket

```bash
gsutil mb gs://videobug-state
# Creating gs://videobug-state/...
```

Create videobug server vm

```shell
gcloud compute instances create-with-container videobug-server --project=shining-camp-344911 --zone=us-west4-b --machine-type=e2-micro --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=999400631328-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --image=projects/cos-cloud/global/images/cos-stable-97-16919-29-21 --boot-disk-size=10GB --boot-disk-type=pd-balanced --boot-disk-device-name=videobug-server --container-image=artpar/videobug-server --container-restart-policy=always --container-privileged --container-arg=--server.port=80 --container-env=INSIDIOUS_AWS_REGION=auto,INSIDIOUS_AWS_ACCESS_KEY=GOOG1EDDOXXOJUVP7BKGC4YKPKIEYAGMXRKULYVP7CBCP7HBDX3VYD76QNMKI,INSIDIOUS_AWS_SECRET_KEY=jRceGAuKW661MSkd0q0VvM/dJ7xp8MXcyx8Rdqeo,INSIDIOUS_AWS_S3_ENDPOINT=https://storage.googleapis.com,INSIDIOUS_JAVAAGENT_URL=https://videobug-dev.s3.ap-south-1.amazonaws.com/java-agent-1.6.7.jar,INSIDIOUS_UPLOAD_BUCKETNAME=videobug-state,INSIDIOUS_UPLOAD_PATH=/uploads --container-mount-tmpfs=mount-path=/uploads --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --labels=container-vm=cos-stable-97-16919-29-21
```


```shell
gcloud compute instances create aerospike-node-1 --project=shining-camp-344911 --zone=us-west4-b --machine-type=e2-standard-2 --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=999400631328-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --create-disk=auto-delete=yes,boot=yes,device-name=aerospike-node-1,image=projects/shining-camp-344911/global/images/aerospike-node-image,mode=rw,size=10,type=projects/shining-camp-344911/zones/us-west4-b/diskTypes/pd-balanced --create-disk=device-name=aerospike-1,mode=rw,name=aerospike-1,size=100,type=projects/shining-camp-344911/zones/us-west4-b/diskTypes/pd-ssd --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any
```


snap set videobug-server s3-endpoint=https://storage.googleapis.com
snap set videobug-server s3-bucketname="videobug-state"
snap set videobug-server s3-access-key=GOOG1EDDOXXOJUVP7BKGC4YKPKIEYAGMXRKULYVP7CBCP7HBDX3VYD76QNMKI
snap set videobug-server s3-secret-key="jRceGAuKW661MSkd0q0VvM/dJ7xp8MXcyx8Rdqeo"



