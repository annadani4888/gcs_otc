# gcs-otc-data-stream

# 📌 Introduction
This tool copies FUSION data from a GCS (Google Cloud Storage) bucket to an OTC (Open Telekom Cloud) bucket.

# 📝 Pre-requisites

1. 📄 GCS credentials (as a json credentials file)
2. 🔑 OTC credentials (access key, secret key, endpoint and bucket name)
 
# Saving credentials as Variables in Gitlab CI/CD

## Step 1: Store the GCS service account JSON file as a CI/CD environment variable

After downloading the GCS credentials.json file from Google Cloud Platform, convert the content of the file to a single-line string in your terminal:

```bash
cat /path/to/your-service-account.json | jq -c . > single-line.json
```

## Step 2: Add the Single-Line JSON to GitLab CI/CD Variables

1. In GitLab, navigate to Settings > CI/CD > Variables.
2. Add a new variable named 'GCS_SERVICE_ACCOUNT_JSON', and paste the content of of 'single-line.json' into it.
3. Save the new Variable.

## Step 3: Add the client's OTC storage credentials to GitLab CI/CD Variables

1. In GitLab, navigate to Settings > CI/CD > Variables.
2. Add a new variable named 'OTC_ACCESS_KEY', and paste the access key credentials into it.
3. Repeat this step for the 'OTC_ENDPOINT', and 'OTC_SECRET_KEY'.

# 🔧 Usage

For adding additional clients to the pipeline, each client would need their own 'remote' in the rclone config file.

## Edit the 'before_script' section of the .gitlab-ci.yml file

```bash
before_script:
    # Install necessary dependencies: rclone, python3 and py3-pip
    - apk add --no-cache rclone python3 py3-pip
    # Create the directory for rclone config file
    - mkdir -p /root/.config/rclone
    # Write the GCS service account JSON to a file
    - echo "$GCS_SERVICE_ACCOUNT_JSON" > /root/gcs-service-account.json
    # Set up the rclone config file and create a GCS remote and OTC remote for Client 1
    - echo "[gcs]" > /root/.config/rclone/rclone.conf
    - echo "type = google cloud storage" >> /root/.config/rclone/rclone.conf
    - echo "service_account_file = /root/gcs-service-account.json" >> /root/.config/rclone/rclone.conf
    - echo "[otc]" >> /root/.config/rclone/rclone.conf
    - echo "type = s3" >> /root/.config/rclone/rclone.conf
    - echo "provider = Other" >> /root/.config/rclone/rclone.conf
    - echo "access_key_id = ${OTC_ACCESS_KEY}" >> /root/.config/rclone/rclone.conf
    - echo "secret_access_key = ${OTC_SECRET_KEY}" >> /root/.config/rclone/rclone.conf
    - echo "endpoint = ${OTC_ENDPOINT}" >> /root/.config/rclone/rclone.conf

    # Setup  OTC remote for Client 2
    - echo "[otc-client2]" >> /root/.config/rclone/rclone.conf
    - echo "type = s3" >> /root/.config/rclone/rclone.conf
    - echo "provider = Other" >> /root/.config/rclone/rclone.conf
    - echo "access_key_id = ${OTC2_ACCESS_KEY}" >> /root/.config/rclone/rclone.conf
    - echo "secret_access_key = ${OTC2_SECRET_KEY}" >> /root/.config/rclone/rclone.conf
    - echo "endpoint = ${OTC2_ENDPOINT}" >> /root/.config/rclone/rclone.conf
    
script:
    # Output the contents of the rclone config file to the job log
    - cat /root/.config/rclone/rclone.conf
    
    # Run the rclone copy command for Client 1
    - rclone copy gcs:gaf-sachsen-anhalt otc:obs-30824-10-5500-planet/fusiondata --checksum --transfers 10 --verbose --progress
    
    # Run the rclone copy command for Client 2
    - rclone copy gcs:your-gcs-bucket otc-client2:client2-obs-bucket --checksum --transfers 10 --verbose --progress
```
The above example for Client2 assumes the bucket for Client1 and Client2 live in the same GCS project; in which case, the service account credentials
file would apply to both cases. If a client's GCS bucket doesn't live in 'planet-services-prod', then a new credentials.json would need to be created, and saved as a 
new Variable in GitLb CI/CD.
