# ☁️The Cloud Resume Challenge : AWS Edition

### Project Overview

---

This project is a customized implementation of the [Cloud Resume Challenge – AWS Edition](https://cloudresumechallenge.dev/docs/the-challenge/aws/), where the objective is to deploy a scalable, serverless resume website using Amazon Web Services (AWS). While the original challenge includes steps such as obtaining the AWS Cloud Practitioner certification and building the resume from scratch, certain aspects were adapted to align with personal preferences and available resources.

### Key Objectives

- **Frontend Development**: Utilized a pre-built Next.js and Tailwind CSS template to create a responsive and visually appealing resume webpage. The content was customized to reflect personal achievements and experiences.
- **Static Website Hosting**: Deployed the static website on Amazon S3, ensuring it is publicly accessible.
- **HTTPS and Custom Domain**: Secured the website by enabling HTTPS using Amazon CloudFront and associated a custom domain name through Amazon Route 53.
- **Visitor Counter with JavaScript**: Implemented a visitor counter on the webpage using JavaScript to track the number of visitors.
- **Backend Development**: Set up a DynamoDB table to store the visitor count.
- **API Integration**: Developed an API using AWS API Gateway and Lambda functions to handle requests from the frontend and interact with the DynamoDB database.
- **Python Programming**: Wrote Python code for the Lambda functions to process requests and update the visitor count in DynamoDB.
- **Infrastructure as Code (IaC)**: Defined and deployed the infrastructure using AWS Serverless Application Model (SAM) or Terraform to automate the setup of resources.
- **Source Control and CI/CD**: Utilized GitHub for source control and set up continuous integration and deployment pipelines using GitHub Actions for both frontend and backend components.
- **Documentation**: Wrote a blog post detailing the process and lessons learned during the project to reflect on the experience and share knowledge.

---

### Tools & Technologies Used

### Frontend Development

- **Next.js**: A React-based framework utilized to build a dynamic and responsive resume webpage.
- **Tailwind CSS**: A utility-first CSS framework employed to style the resume webpage, ensuring a modern and mobile-friendly design.

### Cloud Services

- **Amazon S3**: Used to host the static website, making it publicly accessible over the internet.
- **Amazon CloudFront**: A Content Delivery Network (CDN) service that securely serves the website over HTTPS, improving load times and security.
- **Amazon Route 53**: A scalable Domain Name System (DNS) web service used to manage the custom domain name and route traffic to the CloudFront distribution.
- **AWS Certificate Manager (ACM)**: Employed to request and manage an SSL/TLS certificate for the custom domain, ensuring secure HTTPS connections.
- **Amazon DynamoDB**: A NoSQL database service used to store and retrieve the visitor count data.
- **AWS API Gateway**: A fully managed service that enables the creation and deployment of RESTful APIs to handle HTTP requests from the frontend.
- **AWS Lambda**: A serverless compute service that runs backend code in response to HTTP requests from the API Gateway, interacting with DynamoDB to update and retrieve the visitor count.

### Infrastructure as Code (IaC)

- **Terraform**: An open-source IaC tool used to define and provision the AWS infrastructure, ensuring a reproducible and version-controlled setup.

### Continuous Integration and Deployment (CI/CD)

- **GitHub Actions**: A CI/CD platform integrated with GitHub repositories to automate the build, test, and deployment processes for both frontend and backend components.

### Programming Languages

- **JavaScript**: Used to implement the visitor counter on the frontend webpage, tracking the number of visitors.
- **Python**: Employed in AWS Lambda functions to process HTTP requests, interact with DynamoDB, and update the visitor count.

### Domain Registration and Management

- **Namecheap**: A domain registrar used to purchase and manage the custom domain name for the website.

---

## Detailed Overview

### Step 1: Resume Setup

For this step, I used a professionally designed portfolio website template to create my personal website. The template includes a `My Resume` button, which I customized to open a downloadable version of my resume.

I added my resume as a PDF file inside the public folder of the project. When a user clicks the
`My Resume` button, the site opens the route `/resume/`, which displays the PDF directly in the browser. From there, it can also be downloaded.

Although the Cloud Resume Challenge encourages building the resume directly in HTML, I chose to integrate a downloadable PDF version to reflect a more realistic, professional setup. This still demonstrates frontend skills and attention to user experience while preparing for the backend and deployment portions of the challenge.

---

### Step 2: Hosting the Website on Amazon S3 (Static Website)

To make my portfolio website publicly accessible, I chose **Amazon S3 (Simple Storage Service)** for hosting as a static website. This solution is well-suited for serving static content and integrates smoothly with the rest of the AWS ecosystem.

### What is Amazon S3?

Amazon S3 is a scalable object storage service from AWS that supports hosting static websites. It allows you to store and serve files such as HTML, CSS, JavaScript, and other assets (like images and PDFs). While it does not support server-side functionality, it is an excellent choice for frontend-only applications.

### Prerequisites

Before deploying the site, ensure the following are in place:

- An active [AWS account](https://aws.amazon.com/)
    
- Website files ready for deployment (HTML, CSS, JS, PDFs, etc.)

> Tech Stack: This project was built using Next.js and Tailwind CSS. Since S3 only supports static assets, the Next.js application must be exported to static HTML.

---

### Exporting the Next.js Application for Static Hosting

To make the Next.js app compatible with S3 hosting, I configured it for static export. This required a `next.config.js` file in the root directory with the following configuration:

```
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  output: "export",
  images: {
    unoptimized: true,
  },
  trailingSlash: true, // Ensures all routes generate their own HTML files
};

export default nextConfig;
```

This configuration disables server-side features and optimizes the app for static file generation.

### Build and Export Commands

```bash
npm install        # Install dependencies
npm run build      # Build the Next.js application
npm run export     # Export static files to the /out directory
```

This process generates a `/out` directory containing the static version of the site, ready to be deployed to S3.

---

### Deploying to Amazon S3

### Log In to AWS Console

- Navigate to [aws.amazon.com](https://aws.amazon.com/)
- Sign in to the AWS Management Console

### Access the S3 Service

- Use the search bar to find “S3” and open the service

### Create a New S3 Bucket

- Click “Create bucket”
- Provide a unique bucket name (e.g., `my-portfolio-site`)
- Choose the region closest to your users
- Uncheck **Block all public access**
- Acknowledge the public access warning
- Click “Create bucket”
    

### Upload the Website Files

- Open your newly created bucket
- Add all files from the `/out` directory (e.g., `index.html`, `style.css`, `resume.pdf`)
- Upload your static files
****Upload everything inside the `out/` directory to the bucket.
You can drag-and-drop or use the AWS CLI:
    
    ```bash
    aws s3 sync out/ s3://himanshu-bhatt-portfolio-website
    ```
    

### Enable Static Website Hosting

- Go to the **Properties** tab
- Scroll to **Static website hosting** and click “Edit”
- Enable the hosting option
- Set `index.html` as the index document
- (Optional) Specify `404.html` as the error document
- Click “Save changes”
    

### Set the Bucket Policy to Allow Public Access

- Go to the **Permissions** tab
- Scroll down to **Bucket policy** and click “Edit”
- Paste the following policy (replace `your-bucket-name` accordingly):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

- Click “Save changes”
    

### Access Your Website

- Navigate back to the **Properties** tab
- Under **Static website hosting**, locate the **Website endpoint**
- This URL is the public address for your live website
    

### Step 3: **Purchasing a Custom Domain and Configuring DNS with Route 53**

As part of the AWS Cloud Resume Challenge, I configured my static resume site to use HTTPS with a custom domain purchased from Namecheap. This involved integrating Amazon CloudFront, Route 53, and AWS Certificate Manager (ACM). Below is a detailed breakdown of the steps I followed.

### Purchasing a Custom Domain from Namecheap

The first step was to purchase a domain name that I could use for my resume website:

- Visited [Namecheap](https://namecheap.com/) and searched for an available domain (e.g., `himanshubhatt.co.in`).
- Added the domain to the cart and completed the purchase.
- Left the domain configured with default Namecheap DNS for now, to be updated after setting up Route 53.
    

### Creating a Hosted Zone in Route 53

To manage DNS records through AWS, I set up a hosted zone:

- Opened Route 53 and selected "Create hosted zone".
- Entered `himanshubhatt.co.in` and selected the "Public hosted zone" type.
- Route 53 generated four NS (Name Server) records, which would be needed for DNS delegation.
    

### Step 4: Requesting an SSL Certificate in AWS Certificate Manager

To serve the site over HTTPS, I first requested a public SSL certificate:

- Navigated to AWS Certificate Manager in the AWS Console.
- Clicked "Request a certificate" and selected "Public certificate".
- Entered `himanshubhatt.co.in` as the domain name. (Optionally, `www.himanshubhatt.co.in` was also added.)
- Chose DNS validation for domain ownership verification.
- AWS provided a CNAME record to be added to the domain’s DNS settings for validation.
    

### Step 5: Configuring Namecheap to Use Route 53 DNS

To connect the Namecheap domain to AWS Route 53:

- Logged into my Namecheap account.
- Navigated to Domain List > Domain> Nameservers.
- Under the "Nameservers" section, selected "Custom DNS".
- Entered the four NS records provided by Route 53.
- Saved the configuration and allowed time for DNS propagation.
    

### Step 6: Completing SSL Certificate Validation

Once the domain was pointing to Route 53:

- AWS Certificate Manager detected the DNS record and automatically validated the domain.
- The certificate status changed to "Issued" shortly after.
    

### Step 7: Creating a CloudFront Distribution

To serve the static site securely and efficiently, I configured a CloudFront distribution:

- Opened the CloudFront console and selected "Create distribution".
- Set the origin domain as the S3 static website hosting endpoint (e.g., `my-bucket.s3-website-us-east-1.amazonaws.com`).
- Configured the viewer protocol policy to redirect HTTP to HTTPS.
- Added `himanshubhatt.co.in` as an alternate domain name (CNAME).
- Selected the previously issued SSL certificate from ACM.
- Set the default root object to `index.html`.
- Created the distribution and waited for deployment to complete.
    

### Pointing the Domain to CloudFront via Route 53

To route traffic from the domain to the CloudFront distribution:

- In Route 53, opened the hosted zone for `himanshubhatt.co.in`.
- Created a new record with the following details:
    - Record name: `www`
    - Record type: A (Alias)
    - Alias target: selected the CloudFront distribution
- Saved the record.
    

### Redirecting Root Domain (Optional)

To ensure that `www.himanshubhatt.co.in` redirects to `himanshubhatt.co.in`, I implemented one of the following:

- Created a second CloudFront distribution for `www.himanshubhatt.co.in` configured to redirect all requests to `himanshubhatt.co.in`.

### Step 8: Final Verification

To verify that the setup was successful:

- Visited `https://www.himanshubhatt.co.in` in the browser.
- Confirmed that the resume page loaded correctly.
- Verified that the connection was secure using HTTPS and the custom domain was correctly displayed.
    

### **Step 9: Implementing Visitor Counter Logic in JavaScript**

### Setting Up the Visitor Counter Button

- Developed the "Visitor Counter" button by leveraging existing code. Similar to the "My Resume" button, I placed the Visitor Counter button directly below it for consistency. This involved creating a new component, **VisitorCounter.jsx**, and modifying **Header.jsx** to incorporate the new button placement beneath the "My Resume" button.

### Step 10: **Setting Up Amazon DynamoDB to Store Visitor Count**

- Creating the dynamodb table. Created two items inside of it with two attribute as id of string type with value as visits and another attribute as count of type number with initial value as 0
    

### Step 11: **Establishing IAM Role for Lambda Function**

- Creating a Role for Lambda Function using IAM in AWS. The Role will have two policies attached to it. The one policy is AWSLambdaBasicExecutionRole this policy is AWS Managed. The other policy that we will attach to the LambdaRole is created using Policies tab in the AWS Console. This policy will allow us to GetItem and UpdateItem from the DynamoDB Table. After Creating this policy we will attach this policy to the Role that we created earlier that is LambdaRole  

### Step 12: **Developing AWS Lambda Function to Handle Visitor Count**

- Creating the Lambda Function in Python, that manages the visitor count in the DynamoDB Table.
- This Lambda function is designed to handle HTTP requests related to updating and retrieving a visitor count from a DynamoDB table named `visitor-counter`
- After creating the Lambda Function we will deploy it and test it. If the status code is 200 this means our code is correct. And this will update the count value in the DynamoDB Table
    
    ```python
    # lambda_function.py
    import json
    import boto3
    from decimal import Decimal
    
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('visitor-counter')
    
    def lambda_handler(event, context):
        # Common CORS headers
        cors_headers = {
            'Access-Control-Allow-Origin': '*',  # change '*' to your domain in production
            'Access-Control-Allow-Headers': 'Content-Type',
            'Access-Control-Allow-Methods': 'OPTIONS,POST',
            'Cache-Control': 'no-cache'
        }
    
        # 1) Handle preflight OPTIONS request
        if event.get('httpMethod', '') == 'OPTIONS':
            return {
                'statusCode': 200,
                'headers': cors_headers,
                'body': ''
            }
    
        # 2) Handle POST request to increment visitor count
        try:
            response = table.update_item(
                Key={'id': 'visits'},
                UpdateExpression='SET #count = #count + :inc',
                ExpressionAttributeNames={'#count': 'count'},
                ExpressionAttributeValues={':inc': 1},
                ReturnValues='UPDATED_NEW'
            )
            count = response['Attributes']['count']
    
            return {
                'statusCode': 200,
                'headers': cors_headers,
                'body': json.dumps({'count': int(count)})
            }
    
        except Exception as e:
            print(f"Error updating count: {e}")  # logs to CloudWatch
            return {
                'statusCode': 500,
                'headers': cors_headers,
                'body': json.dumps({'error': str(e), 'count': 0})
            }
    
    ```
    

### Step 13: **Creating API Gateway to Expose Lambda Function**

- We will Create a API in API Gateway of REST API Type.
- After creating the api of type REST API we have to firstly create a resource the resource will be called `/counter`
- After creating the  `/counter` resource we will create a method of type POST and for this method we will select the integration type of Lambda and next we have to specify the region where our lambda function is located and we have to specify the lambda function.
- After creating the method POST we have to enable CORS to allow Client to be able to communicate with the apigateway.
- After this we need to create a deployment. This will give us the url and we will provide this url to our next.js frontend application so that the frontend application is able to communicate with the apigateway. And thus retrieve and update the value of count in the DynamoDB Table.  

### Step 14: **Setting Up Version Control with GitHub**

In this Step we will setup our version control for our backend and our frontend code. For our backend we will use terraform to provision our infrastructure and we will create our infrastructure using GitHub Action. And for frontend we will have two repository that will be used to deploy the latest changes that we have made in our website. In this two repository approach we will have two repositories one public and one private. The public repository will contain the workflow yaml file that will actually commit the changes that we made in our portfolio website to the appropriate s3 bucket and using that workflow we will also invalidate CloudFront cache. So that the updated website is visible and accessible. On the other hand the private repository will contain the frontend build that we generated out of our next.js portfolio template and it will be a private repository. Rather than committing the complete code to the private repository we will first build the frontend code locally and then we will push the `/out` folder that get’s generated as the frontend build

### Step 15: **Implementing CI/CD Pipeline for Backend with GitHub Actions**

Now for backend we want to move from provisioning the infrastructure manually to provisioning it from terraform so for that we will import the existing provisioned infrastructure in aws to the terraform configuration files. And once this is done moving further the any changes made to the infrastructure will be done using these terraform configuration files. In this terrafrom configuration files we will manage the provisioning of DynamoDB table, Lambda Function, Role for Lambda Function and Api Gateway.

- So first of all we will create a s3 bucket for backend and DynamoDB table for state locking manually using terminal and then we will copy the existing backend to the newly created backend.
    
- Next we will create the workflow using github action that we will execute using github actions to provision the infrastructure using github itself.
    
    ```yaml
    name: "Terraform Deploy"
    
    on:
      push:
        branches:
          - main
    
    permissions:
      contents: read
      pull-requests: write
    
    jobs:
      terraform:
        name: "Terraform"
        runs-on: ubuntu-latest
    
        defaults:
          run:
            shell: bash
            working-directory: ./Infrastructure Setup
    
        steps:
          - name: Checkout Repository
            uses: actions/checkout@v4
    
          - name: Configure AWS Credentials
            uses: aws-actions/configure-aws-credentials@v4
            with:
              aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
              aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
              aws-region: ap-south-1
    
          - name: Setup Terraform
            uses: hashicorp/setup-terraform@v2
            with:
              terraform_version: "1.11.3"
    
          - name: Terraform Format
            run: terraform fmt -check
            continue-on-error: true
    
          - name: Terraform Init
            run: terraform init
    
          - name: Terraform Validate
            run: terraform validate
    
          - name: Terraform Plan
            if: github.event_name == 'pull_request'
            run: terraform plan -no-color
            continue-on-error: true
    
          - name: Terraform Apply
            if: github.ref == 'refs/heads/main' && github.event_name == 'push'
            run: terraform apply -auto-approve
    
    ```
    
    After building the changes in workflow file we will push it to the github and this will trigger the workflow file.
    
- Now since we already created the infrastructure through the aws console manually it in the terraform configuration files we are tring to import that infrastructure and manage it through those files this will make no changes in the infrastructure and it will show:
**No changes. Your infrastructure matches the configuration.
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.**
- And now we are managing our infrastructure through terraform configuration files.

### Step 16: I**mplementing CI/CD Pipeline for Frontend with GitHub Actions**

As I am utilizing a paid portfolio website template, it would not be advisable to commit the frontend code to version control. To manage the CI/CD pipeline for the frontend, I will maintain two GitHub repository, one private and one public. Changes made to this private repository will be pushed to the associated S3 bucket, using the workflow that we store and execute from public repo, which will then trigger the pipeline and automatically reflect the updates in the S3-hosted website.

### Create a private github repo and push the frontend build.

- In this Private GitHub Repo we will store the build of our frontend folder. We can make the changes in our frontend code. And then we can run the command:
    
    ```jsx
    npm run build
    ```
    
    By running this command it will create a frontend build and will generate a  `/out` folder that contains our frontend build. And then we will push this  `/out` folder to this private repo.
    

    
    This will only allow  `/out` to be pushed to GitHub Repo.
    

    

### Create the public github repo  and configure the OIDC for github

- Now we will create a public GitHub Repo which will store the workflow yaml file and this workflow yaml file will push the contents of the `/out` folder to the appropriate S3 bucket and thus we will have the required and updated changes in our website and rather than configuring the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY we will setup OIDC for GitHub.
- So Firstly we will create a OIDC Identity Provider
    

    
- Next we will create a role that trusts GitHub
    

    
- Next we will attach permissions to the role (GitHub-OIDC-Role) that we have created.
We will attach two polices to this GitHub-OIDC-Role.
- AmazonS3FullAccess: So that we can push the frontend build to the S3 bucket when GitHub Assume this role
- CloudFrontFullAccess: So that we can invalidate the cache and that cloudfront takes the latest updated changes.
    

    
- Next we will update the Trust Policy that defines who can assume this role:
    

    

### Create the Required tokens for the Workflow

- Now we will setup the personal access token (PAT) and CloudFront Distribution Id as secrets so that we can authenticate to our private repo, pull the code from the private repo and invalidate the CloudFront Cache
    
    

### Create the Workflow to deploy the frontend build to the S3 Bucket

- After setting up the PAT we will create the workflow.
    
    ```yaml
    name: Deploy Next.js Static Export to S3
    on:
      workflow_dispatch:
    
    jobs:
      deploy:
        runs-on: ubuntu-latest
    
        permissions:
          id-token: write
          contents: read
    
        steps:
        - name: Checkout the repo
          uses: actions/checkout@v4
    
        - name: Setup Node.js
          uses: actions/setup-node@v4
          with:
            node-version: '20'
    
        - name: Clone Private frontend build Repo
          run: |
            git clone https://dmz-v-x:${{secrets.PRIVATE_REPO_PAT}}@github.com/dmz-v-x/Cloud-Resume-Challenge-Frontend-Build.git
    
        - name: Configure AWS credentials via OIDC
          uses: aws-actions/configure-aws-credentials@v4
          with: 
            role-to-assume: arn:aws:iam::590183681939:role/GitHub-OIDC-Role
            aws-region: ap-south-1
    
        - name: Upload to S3
          run: |
            aws s3 sync Cloud-Resume-Challenge-Frontend-Build/out/ s3://himanshu-bhatt-portfolio-website --delete --exact-timestamps
            
        - name: Invalidate CloudFront
          run: |
            aws cloudfront create-invalidation --distribution ${{secrets.CLOUDFRONT_DISTRIBUTION_ID}} --path "/*"
        
    ```
    

### Run the Workflow and Verify the successful deployment of new build to the S3 Bucket

- After workflow creation is done we will run the workflow:
    

    
- And thus now it shows the updated portfolio website with the VisitorCounter.
    

    

### Step 17: Conclusion to Cloud Resume Challenge:

### **Portfolio Website Setup Using a Paid Next.js Template**

- **Objective:** Develop a modern, responsive portfolio website to showcase my resume.
- **Approach:** Purchased a premium Next.js template and customized it to align with my personal branding and content requirements.

### **Hosting the Website on Amazon S3**

- **Objective:** Deploy the static website for global accessibility.
- **Approach:** Created an S3 bucket configured for static website hosting, uploaded the website files, and ensured public read access for all users.

### **Purchasing a Custom Domain and Configuring DNS with Route 53**

- **Objective:** Assign a custom domain to the portfolio website for a professional appearance.
- **Approach:** Purchased a domain from Namecheap and configured Route 53 to manage DNS settings, ensuring seamless integration with the S3-hosted website.

### **Setting Up SSL Certificate for the Domain**

- **Objective:** Secure the website with HTTPS to protect user data and enhance trustworthiness.
- **Approach:** Utilized AWS Certificate Manager to request and validate an SSL certificate for the custom domain, enabling secure communication.

### **Configuring Amazon CloudFront for the Website**

- **Objective:** Distribute the website globally with low latency and high transfer speeds.
- **Approach:** Set up a CloudFront distribution with the S3 bucket as the origin, configured SSL settings, and enabled HTTP to HTTPS redirection.

### **Implementing Visitor Counter Logic in JavaScript**

- **Objective:** Track and display the number of visitors to the website.
- **Approach:** Developed a JavaScript function that fetches and displays the current visitor count from the backend API.

### **Setting Up Amazon DynamoDB to Store Visitor Count**

- **Objective:** Persistently store the visitor count data.
- **Approach:** Created a DynamoDB table with appropriate partition keys and provisioned throughput to handle expected traffic.

### **Establishing IAM Role for Lambda Function**

- **Objective:** Grant necessary permissions for the Lambda function to interact with DynamoDB.
- **Approach:** Defined an IAM role with policies allowing read and write access to the DynamoDB table, ensuring secure operations.

### **Developing AWS Lambda Function to Handle Visitor Count**

- **Objective:** Process API requests to retrieve and update the visitor count.
- **Approach:** Wrote a Python Lambda function using the `boto3` library to interact with DynamoDB, implementing logic to increment and fetch the visitor count.

### **Creating API Gateway to Expose Lambda Function**

- **Objective:** Provide a secure and scalable endpoint for the frontend to interact with the Lambda function.
- **Approach:** Set up an HTTP API in API Gateway, integrated it with the Lambda function, and configured CORS settings to allow cross-origin requests.

### **Implementing Infrastructure as Code with Terraform**

- **Objective:** Automate the deployment of AWS resources to ensure consistency and repeatability.
- **Approach:** Defined the infrastructure components, including S3, CloudFront, ACM, Route 53, Lambda, API Gateway, and DynamoDB, using Terraform configuration files.

### **Setting Up Version Control with GitHub**

- **Objective:** Track changes to the codebase and collaborate effectively.
- **Approach:** Initialized a Git repository for the project, committed the code, and pushed it to a GitHub repository, ensuring proper `.gitignore` configurations.

### **Implementing CI/CD Pipeline for Backend with GitHub Actions**

- **Objective:** Automate testing and deployment of backend changes.
- **Approach:** Configured GitHub Actions workflows to run unit tests on code push, and upon successful tests, deploy the infrastructure using Terraform and update the Lambda function.

### **Implementing CI/CD Pipeline for Frontend with GitHub Actions**

- **Objective:** Automate deployment of frontend updates to the S3 bucket.
- **Approach:** Set up GitHub Actions workflows to build the frontend, deploy it to S3, and invalidate the CloudFront cache to reflect changes immediately.

Thus these are all the steps that i took to complete my Cloud Resume Challenge. Completing the AWS Cloud Resume Challenge has significantly enhanced my practical skills in cloud architecture, automation, and DevOps practices. This hands-on experience has not only solidified my understanding of AWS services but also demonstrated my ability to apply them in real-world scenarios.

---

### Reference & Resources

**Official Challenge Documentation:**

- The Cloud Resume Challenge – AWS Edition:
https://cloudresumechallenge.dev/docs/the-challenge/aws/

**Project Repository:**

- GitHub Repository:

  https://github.com/dmz-v-x/Cloud-Resume-Challenge-Frontend-Deploy-Pipeline

  https://github.com/dmz-v-x/Cloud-Resume-Challenge-Backend-Deploy-Pipeline
