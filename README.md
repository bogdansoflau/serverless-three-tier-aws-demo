# Build a Three-Tier Web App on AWS

![Image](https://i.imgur.com/HhuEWsX.png)

**Author:** Bogdan Soflau 
**Email:** bogdansoflau01@gmail.com

---

## Build a Three-Tier Web App

![Image](https://i.imgur.com/wgeFEd3.png)

---


## Overview

In this project, I implemented a fully functional three-tier web application using AWS. The architecture includes:

- **Presentation Tier:** Amazon S3 + CloudFront 
- **Logic Tier:** AWS Lambda + API Gateway 
- **Data Tier:** Amazon DynamoDB

The final setup allows users to query user data via a web interface, with serverless backend processing and persistent storage.

---

## Technologies & Concepts

- **Services:** S3, CloudFront, Lambda, API Gateway, DynamoDB 
- **Key Concepts:** Three-tier architecture, CORS configuration, serverless functions, origin access control, API integration 

---

## Architecture Breakdown

### Presentation Tier

The presentation tier hosts the frontend assets (HTML, CSS, JavaScript) in an S3 bucket and distributes them via CloudFront. 
To ensure secure access, I configured Origin Access Control (OAC) so that only CloudFront can retrieve content from the S3 bucket.

**Result:** The frontend is publicly accessible via the CloudFront distribution URL.

![Image](https://i.imgur.com/gWMHHib_d.png?maxwidth=520&shape=thumb&fidelity=high)

---

###  Logic Tier

I developed a Lambda function that receives API requests from users via API Gateway. This function processes input (e.g., `userId`) and queries the DynamoDB table for related data.

**Highlights:**

- API Gateway acts as the interface between frontend and Lambda 
- The Lambda uses the AWS SDK to access DynamoDB 
- JSON responses are returned to the frontend

![Image](https://i.imgur.com/mtqXsT7.png)

---

### Data Tier

The data tier is built with Amazon DynamoDB. The table stores user data, indexed by `userId` as the partition key.

This schema ensures efficient key-based lookups when queried by the Lambda function.


![Image](https://i.imgur.com/XbJg7GK.png)

---

## Logic and Data tier

After setting up all three tiers, the final step was to connect the presentation tier to the backend logic and data.

To validate the connection, I tested the API Gateway's prod stage invoke URL directly in the browser by entering a sample `userId`. The API successfully returned the corresponding user data in JSON format, confirming that the Lambda function could query the DynamoDB table correctly.

---
## Console Errors

Initially, the frontend (served via CloudFront) failed to fetch data. The issue stemmed from the `script.js` file: it still referenced a placeholder API URL instead of the actual prod stage invoke URL.

###  Fix:
- Updated `script.js` with the correct API URL
- Re-uploaded the file to the S3 bucket

However, after correcting the URL, the browser console displayed a CORS (Cross-Origin Resource Sharing) error. This occurred because API Gateway, by default, only allows requests from the browser directly — not via the CloudFront domain.


![Image](https://i.imgur.com/PLgTn8y.png)

---

## Resolving CORS Errors

To resolve this CORS error, I went into the API Gateway and enabled CORS on the /users resource. I then made sure GET requests are enabled, and referenced the CloudFront domain as the domain getting access.

I also updated the Lambda function because it needed to be able to return CORS headers to show that it has the permission to invoke the API's invoke URL and return a response.

![Image](https://i.imgur.com/yMIZxtA.png)

---

## Fixed Solution

To fix the CORS issue and allow the frontend to access the API, I completed the following steps:

1. **API Gateway:** 
   - Enabled CORS on the `/users` resource 
   - Allowed `GET` requests 
   - Whitelisted the CloudFront domain in `Access-Control-Allow-Origin`

2. **Lambda Function:** 
   - Updated response headers to explicitly include `Access-Control-Allow-Origin` and `Access-Control-Allow-Headers`

---

## Conclusion

This project demonstrates the implementation of a scalable and serverless three-tier architecture using AWS. The hands-on experience with CORS, Lambda functions, API Gateway, and DynamoDB provided deeper insight into building real-world cloud-native applications
---
