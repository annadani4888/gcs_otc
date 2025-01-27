## GCS-to-OTC Data Synchronization Script

This repository contains a CI/CD pipeline script designed to automate the transfer of data from a Google Cloud Storage (GCS)  bucket to an Open Telekom Cloud (OTC) or CreoDias bucket using rclone. The pipeline runs on a schedule and is designed for seamless integration within GitLab CI/CD.

**Key Features**

-   **Automated Data Transfer**:
    -   Transfers data between cloud platforms using `rclone`, ensuring compatibility and reliability.
-   **CI/CD Integration**:
    -   Designed as a GitLab CI/CD pipeline to execute recurring data transfers.
-   **Platform-Agnostic Configuration**:
    -   Supports both OTC and CreoDias endpoints, configured dynamically based on environment variables.
-   **Parallel Transfers**:
    -   Optimized for faster data transfers using `--transfers` and `--checksum` flags

**Pipeline Overview**

The pipeline is defined in a `.yml` file and includes the following key components:

1.  **Image**:
    
    -   Utilizes the lightweight **Alpine Linux** image to minimize build times.
    -   Installs dependencies like `rclone` and `python3` at runtime.
2.  **Stages**:
    
    -   Single `sync` stage to handle the data transfer.
3.  **Environment Variables**:
    
    -   **`GCS_SERVICE_ACCOUNT_JSON`**: JSON credentials for GCS.
    -   **`OTC_ACCESS_KEY`**: Access key for OTC.
    -   **`OTC_SECRET_KEY`**: Secret key for OTC.
    -   **`OTC_ENDPOINT`**: Endpoint for the OTC or CreoDias S3 bucket.
4.  **Dynamic Configuration**:
    
    -   Creates an `rclone` configuration file at runtime using the provided environment variables.
5.  **Transfer Logic**:
    
    -   Runs the `rclone copy` command to transfer data from GCS to OTC/CreoDias with checksum verification and parallel transfers.

**Usage**
### **1. Setup Environment Variables**

Before running the pipeline, ensure the following environment variables are set in GitLab CI/CD:

-   **`GCS_SERVICE_ACCOUNT_JSON`**: Contents of the GCS service account JSON file.
-   **`OTC_ACCESS_KEY`**: OTC access key.
-   **`OTC_SECRET_KEY`**: OTC secret key.
-   **`OTC_ENDPOINT`**: S3-compatible endpoint for OTC or CreoDias.

### **2. Configure the Pipeline**

Include the `.yml` file in your GitLab project. The pipeline is triggered on a scheduled basis, defined in the GitLab CI/CD settings.

**Acknowledgments**

This script utilizes rclone, a powerful cross-cloud synchronization tool, to facilitate seamless data transfer between GCS and OTC or CreoDias.
