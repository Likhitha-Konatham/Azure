# Azure Storage Services

## Azure Blob Storage

Azure Blob Storage is used to store **large files** in the cloud. These files can be images, videos, logs, backups, or documents. It stores data as **objects (blobs)** inside **containers**, and every file gets its own URL.

### When to use it?

* When you need to store large unstructured data
* When files don’t need to be edited directly
* When you want cheap and scalable storage

Examples:

* Application logs
* Backup files
* Images and videos for websites

A DevOps engineer can store **build artifacts** (JAR, WAR, ZIP files) generated from CI pipelines in Blob Storage. During deployment, these artifacts can be downloaded from Blob Storage using Azure CLI or pipeline tasks.

### AWS Equivalent

* **Amazon S3**

---

## Azure File Storage

Azure File Storage provides a **shared file system** in the cloud. It works like a normal file server and supports **SMB protocol**, so multiple VMs or applications can access the same files.

### When to use it?

* When multiple servers need access to the same files
* When applications expect a file system (read/write files)

Examples:

* Shared configuration files
* Application data shared across VMs

A DevOps engineer can store **application config files** in Azure File Storage and mount it to multiple VMs or containers so all instances use the same configuration.

### AWS Equivalent

* **Amazon EFS (Elastic File System)**

---

## Azure Table Storage

Azure Table Storage is a **NoSQL database** used to store structured or semi-structured data in a **key-value format**. It is very fast and highly scalable.

### When to use it?

* When you don’t need complex queries
* When data is simple and accessed using keys

Examples:

* App configuration values
* User metadata
* Environment settings

A DevOps engineer can store **environment-specific settings** (like feature flags or service URLs) in Table Storage and fetch them during deployments or application startup.

### AWS Equivalent

* **Amazon DynamoDB** (similar concept)

---

## Azure Queue Storage

Azure Queue Storage is used to send and receive **messages** between different parts of an application. It helps applications work **asynchronously**.

### When to use it?

* When tasks should run in background
* When services should not depend on each other directly

Examples:

* Order processing
* Background jobs
* Email notifications

A DevOps engineer can use Queue Storage to trigger background jobs like image processing, log processing, or batch tasks after deployment.

### AWS Equivalent

* **Amazon SQS (Simple Queue Service)**

---

## Comparison Table

| Azure Service | Purpose              | AWS Equivalent |
| ------------- | -------------------- | -------------- |
| Blob Storage  | Store large files    | S3             |
| File Storage  | Shared file system   | EFS            |
| Table Storage | NoSQL key-value data |       |
| Queue Storage | Message queue        | SQS            |

---

### Summary

* **Blob** → Store files
* **File** → Share files
* **Table** → Store simple data
* **Queue** → Send messages
