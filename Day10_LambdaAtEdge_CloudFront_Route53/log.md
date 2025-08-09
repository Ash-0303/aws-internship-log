# Day 10 – Serverless Web Delivery with Lambda@Edge, CloudFront & Route 53

**Date:** 15th July 2025  
**Internship Role:** Cloud Support Engineer Intern  
**Platform:** Amazon Web Services (AWS)  

---

## Task Summary
- Built a serverless, globally distributed web delivery pipeline using:
  - **Amazon CloudFront** – CDN for global content delivery
  - **Lambda@Edge** – Serverless function at CloudFront edge locations
  - **Amazon Route 53** – DNS routing to CloudFront distribution
  - **Application Load Balancer (ALB)** – Origin for backend content
- Implemented a Lambda@Edge function to add a custom HTTP response header.
- Linked custom domain `ashwin0303.com` to CloudFront via Route 53.
- Troubleshot **502** and **503** errors during deployment.

---

## Services Overview

| Service           | Purpose |
|-------------------|---------|
| **CloudFront**    | Delivers cached/static content from edge locations worldwide |
| **Lambda@Edge**   | Customizes requests/responses at edge without modifying the origin |
| **Route 53**      | Routes domain name to CloudFront distribution |
| **ALB**           | Backend origin serving dynamic content |

---

## Lambda@Edge Event Types
- **Viewer Request** – Before CloudFront checks cache (redirects, rewrites, auth).
- **Origin Request** – Before CloudFront fetches from origin (modify path, add headers).
- **Origin Response** – After CloudFront gets origin response (transform, add headers).
- **Viewer Response** – Before sending final content to viewer (tracking, clean-up).

---

## Implementation Steps

### **Step 1 – Create SSL Certificate (us-east-1)**
1. ACM → Request public certificate for `ashwin0303.com`.
2. DNS validation via CNAME records in Route 53 hosted zone.
3. Wait for certificate issuance.

### **Step 2 – Create CloudFront Distribution**
- **Origin Domain:** ALB DNS name
- **Origin Protocol Policy:** HTTPS only (later changed to HTTP during troubleshooting)
- **Viewer Protocol Policy:** Redirect HTTP to HTTPS
- **Alternate Domain (CNAMEs):** `ashwin0303.com`
- **SSL Certificate:** Use ACM certificate from Step 1
- **Cache Policy:** `CachingDisabled` for debugging

### **Step 3 – Create IAM Role for Lambda@Edge**
- Role name: `LambdaEdgeExecutionRole`
- Permissions: `AWSLambdaBasicExecutionRole`
- Trust policy includes `lambda.amazonaws.com` and `edgelambda.amazonaws.com`.

### **Step 4 – Create Lambda@Edge Function**
- Name: `EdgeResponseModifier`
- Runtime: Node.js 18.x
- Example Code:
  ```javascript
  exports.handler = async (event) => {
      const response = event.Records[0].cf.response;
      const headers = response.headers;
      headers['x-powered-by'] = [{ key: 'X-Powered-By', value: 'Lambda@Edge' }];
      return response;
  };
  ```
- Publish a version.
- Attach to CloudFront behaviour (Origin Response trigger).

### **Step 5 – Route 53 Configuration**
- Create/update **A Record (Alias)** pointing `ashwin0303.com` to CloudFront distribution.

---

## Troubleshooting & Fixes

### **Issue 1 – 503 LambdaExecutionError**
- **Error:** Function was saved as `.mjs` but written in CommonJS format.
- **Fix:** Renamed file to `index.js`, redeployed, published new version, re-attached to CloudFront.

### **Issue 2 – 502 Bad Gateway**
- **Error:** CloudFront origin protocol set to HTTPS only, but ALB only accepted HTTP on port 80.
- **Fix:** Changed CloudFront Origin Protocol Policy to HTTP only and set port to 80.

---

## Outcome
- Lambda@Edge successfully modifies responses at the edge.
- CloudFront reliably connects to ALB.
- Domain `ashwin0303.com` serves content globally with custom header:  
  ```
  X-Powered-By: Lambda@Edge
  ```

---

## Future Consideration
- Configure ALB with an HTTPS listener and valid ACM certificate.
- Change CloudFront origin protocol back to HTTPS for secure end-to-end delivery.