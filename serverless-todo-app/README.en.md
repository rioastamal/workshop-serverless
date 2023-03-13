<a name="top"></a>

<!-- begin step-0 -->

## Serverless Todo App Workshop

In this workshop, you will create a Todo application built on a serverless architecture. The backend of the application is written in Node.js and the frontend is written in HTML/Javascript/Vue.js.

The following services are utilized on the backend:

- AWS Lambda to run Node.js code
- Amazon API Gateway as proxy or routing to APIs (Lambda)
- Amazon DynamoDB for databases (NoSQL)
- Amazon SQS for queues
- AWS Systems Manager to store secret parameters
- Amazon SES for sending emails
- Amazon S3 to save the zip of the Lambda function before deployment

The following services are utilized on the frontend:

- AWS Amplify for hosting static HTML/Javascript/Vue.js websites

Another utilized services is:

- GitHub as a Git repository to store the code used in the workshop

![serverless-workshop-monolith](https://user-images.githubusercontent.com/469847/224513703-a4abbd98-a208-4dba-9569-a8dc41d5e688.png)

> Overview of application architecture

<hr>
<!-- end step-0 -->

<details>
  <summary><h2>Module 1 - Preparation</h2></summary>
  
  <details>
    <summary><h3>Sign in to the AWS Console</h3></summary>

> **IMPORTANT**
\
If you are using your own AWS account then you can simply go to https://aws.amazon.com/ and enter your credentials to enter the AWS Console.

When attending the workshop with the AWS account that has been set up by the event organizer, follow the steps below to enter the AWS Console.

1. Go to the [workshop portal](https://dashboard.eventengine.run) page provided by the event organizer. Enter the hash code that was given to you.

![Event Engine Dashboard](https://user-images.githubusercontent.com/469847/222730689-22b6de6d-0d25-4d8c-8679-05f38c8adabb.png)

2. Choose **Email One-time Password (OTP)**

![Email OTP](https://user-images.githubusercontent.com/469847/222732114-f0b5eb15-e118-4530-9ac5-5a4eb67a283f.png)

3. Enter your email then select **Send passcode**. Then check your email to see the OTP sent.

> **IMPORTANT**: If the email is not found, also check the spam/junk folder

![Email address](https://user-images.githubusercontent.com/469847/222732276-645740a8-5211-47c0-b8d7-a697e2edfb03.png)

4. Enter the 9 digits sent to your email, then select **Sign in**

![Passcode](https://user-images.githubusercontent.com/469847/222732419-2a04776d-5230-468a-91b1-a7d3a598f240.png)

5. Select **AWS Console**, then **Open Console**

![AWS Console button](https://user-images.githubusercontent.com/469847/222732539-ff826a9b-dc5f-4c47-a428-4a80ef6e6726.png)

![Open Console button](https://user-images.githubusercontent.com/469847/222732664-8d98ce50-8ac1-48b5-923c-679bb6ba86a0.png)

You will be taken to the AWS Console dashboard page.

  </details>
  <!--Sign in to the AWS Console-->
  
  <details>
    <summary><h3>Example Navigation in AWS Console</h3></summary>

You can get to a service page quickly by typing the name of the service in the **Search** input.

![Search on AWS Console](https://user-images.githubusercontent.com/469847/222456956-502b8cdd-1e03-4496-b41b-545daeeab8c5.png)

Then you can select the Service from the search results. You can also open it in a new browser tab for easier future navigation.

![Search AWS Lambda](https://user-images.githubusercontent.com/469847/222457768-0a012b86-d18f-448d-ad06-8df58f9182e2.png)

If you want to do a bookmark service so that it always appears at the top select the **Star**.

  </details>
  <!-- /Example Navigation in AWS Console -->

  <details>
  <summary><h3>Using the AWS Cloud9 Cloud IDE</h3></summary>

AWS Cloud9 is a cloud-based IDE that provides a text editor feature, access to the Terminal to run the shell and a built-in debugger. All that is needed to run AWS Cloud9 is a web browser.

In this workshop you will use Cloud9 to run commands in the terminal and do code editing.

1. Enter [AWS Cloud9](https://console.aws.amazon.com/cloud9control/home)
2. Select **Create environment**
![AWS Cloud9 - Create environment](https://user-images.githubusercontent.com/469847/224514038-926afe2e-c4bf-4878-9aa4-f24c7655464f.png)
3. In **Name**, fill in &quot;workshop-{{NICKNAME}}&quot; my example **workshop-rioastamal**
4. On **Environment type** select **New EC2 instance** on **Instance type** select **t3.small**
5. On **Platform**, select **Amazon Linux 2**, **Timeout** select **4 hours**
6. In **Network Settings** section **Connection** select **AWS Systems Manager (SSM)**
7. Leave the rest at the default value, select **Create**

Wait a few moments for the environment creation process to complete. Select **Open** next to the name of the newly created environment.

![New Cloud9 IDE Environment](https://user-images.githubusercontent.com/469847/222607823-990285f8-ca16-49e4-8a40-707fed4935d8.png)

You will get a Cloud9 view. The default layout on the left is the file manager, in the middle is the file editor and at the bottom is the Terminal window.

![Tampilan AWS Cloud9](https://user-images.githubusercontent.com/469847/222608860-9fcc210e-06a1-4c4d-a897-b5dbf79163c0.png)

> You can resize each window pane by dragging on the edge of the border. Please close the Welcome file.

#### Running Bootstrap Scripts

Now run the following command in AWS Cloud9 Terminal to install some packages required during the workshop.

```sh
curl -s 'https://gist.githubusercontent.com/rioastamal/e0882594e6b34aedf03a56a6efc0b7c0/raw/12af5c42f3468b284accc8222eab70d2a539db12/bootstrap-cloud9-workshop.sh' | bash
```

Check the major version of AWS CLI, it should be 2.x.y.

```sh
aws --version
```

```
aws-cli/2.11.2 Python/3.11.2 Linux/4.14.305-227.531.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off
```

Check the Node version, it should be 16.x.y

```sh
node --version
```

```
v16.19.1
```

  </details>
  <!-- /Using the AWS Cloud9 Cloud IDE -->

  <details>
    <summary><h3>Upload Public SSH Keys to GitHub</h3></summary>

To push the repository, you need to create an SSH key on Cloud9. You need to enter this public SSH key in the settings on GitHub. Run the following command to generate SSH Key.

```
ssh-keygen
```

You can leave blank for all questions by just hit ENTER.

> **IMPORTANT**: This command will overwrite the ~/.ssh/id_rsa file. Pay attention to this when running it on a local computer or in an existing environment.

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ec2-user/.ssh/id_rsa.
Your public key has been saved in /home/ec2-user/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:sEXfWVTXPucWIB57xB6n1bNnlkLgnLCcvw/KtCuZ1iw ec2-user@ip-172-31-30-222
The key's randomart image is:
+---[RSA 2048]----+
|        .. .o.o.=|
|       ...*+.O ++|
|      . .+o+X *.+|
|       +  .o = =*|
|      . S  .. .+=|
|            .   o|
|        =. o   . |
|       Eooo o    |
|      . o=.  .   |
+----[SHA256]-----+
```

It will create two files `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` in the `~/.ssh/` directory.

```sh
ls -l ~/.ssh
```

```
total 12
-rw------- 1 ec2-user ec2-user  991 Feb  21 01:17 authorized_keys
-rw------- 1 ec2-user ec2-user 1679 Feb  21 02:40 id_rsa
-rw-r--r-- 1 ec2-user ec2-user  407 Feb  21 02:40 id_rsa.pub
```

#### Copy the contents of ~/.ssh/id_rsa.pub

```sh
cat ~/.ssh/id_rsa.pub
```

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCqeyLR64hC6OV1RLldH25q07ZribVthVjgcP0Pa8DXS5bcQDMhbNsTLZyfCsLZJZ8ajzNM2lIEndFGMsrLElWNjMMEnyfOV7AMZ/cyq7ult90RjnYgLUtn6Ju1FFQBCIEm6qlxoYjHR7NVkKqC6akAKe6PVwLsJ2XrmCy+ta1oZVY65pfLqvfg5Q0vFE84kmEldalfNm2aGTWwZeEdJ9ngL+dHPdzW2WRk7GaPBbgzBsqepyplAm5fjPPopWyE1W8Eqzb8iSaTcApBKZLdccZCvRb1bEQFcnL5ToSZzhyyXuZfsVn9U6FpDkqBtzPf9GWXfuXrLzgyuNS1jNJBT+3H ec2-user@ip-172-31-26-248.ap-southeast-1.compute.internal
```

> **NOTES**: The contents of your ~/.ssh/id_rsa.pub file must be different from mine.

We will enter the public key into the GitHub account.

1. Open your GitHub account go to **Settings**

![GitHub Settings](https://user-images.githubusercontent.com/469847/222455669-2a5234b8-3680-42d3-8df5-d5e266237c43.png)

2. Select **SSH and GPG keys**, select **New SSH key**
3. In the title, enter "awsug-workshop-cloud9"
4. On **Key type** select **Authentication Key**
5. Paste the contents of `~/.ssh/id_rsa.pub` into the input **Key**
6. Select **Add SSH key**

After the process is complete, you should be able to push to the repository on your GitHub account.

#### Configure Name and Email for Commit

This configuration is useful for giving commits a name and email that matches your GitHub account.

> **IMPORTANT**: Replace {{NAME}} {{EMAIL}} with the email you use on GitHub.

```
git config --global user.name "{{NAME}}"
git config --global user.email "{{EMAIL}}"
```

```sh
git config -l
```

```
credential.helper=!aws codecommit credential-helper $@
credential.usehttppath=true
core.editor=nano
user.name=Rio Astamal
user.email={{EMAIL_ANDA}}
```

  </details>
  <!-- /Upload Public SSH Keys to GitHub -->

</details>
<hr>

<details>
  <summary><h2>Module 2 - Create Basic Services</h2></summary>
  
  <details>
    <summary><h3>Create S3 Buckets</h3></summary>

This bucket will store Lambda function code which will then be deployed via the AWS Lambda console page.

1. Go to [Amazon S3](https://s3.console.aws.amazon.com/s3/home) page. You can do this by entering _Search_ at the top of the AWS console and then typing "S3" into it. - select **S3** - select **Create bucket**
![Create S3 Bucket](https://user-images.githubusercontent.com/469847/224529647-bb97b18d-ae15-48c2-887a-134c2edd6d87.png)
2. In **Bucket name** fill in "serverless-workshop-{{YYYYMM}}-{{NICKNAME}}".
     - Replace {{YYYYMM}} with month year, for example for March 2023 use **202303**.
     - Replace {{NICKNAME}} with your name or something unique. Only input alphanuric, for example if my name is Rio Astamal then use **rioastamal**.
     - Full example for S3 Bucket name **serverless-workshop-202303-rioastamal**
3. In **AWS Region** select _Asia Pacific (Singapore) ap-southeast-1_
4. Leave the other options with default values, then select the **Create bucket** button

You should now have a new bucket, my example is **serverless-workshop-202303-rioastamal**.

![new S3 Bucket](https://user-images.githubusercontent.com/469847/222464525-2cec1ae9-f98d-40af-8dcd-c25b7cd63525.png)

  </details>
  <!-- Create S3 Buckets -->

  <details>
    <summary><h3>Create DynamoDB Tables</h3></summary>

We will use Amazon DynamoDB to store user data and todo lists. For that, you need to create a new DynamoDB Table.

Here we use only one table and apply the [Single Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/) concept to DynamoDB.

1. Go to the Amazon DynamoDB page. In **Search** type "dynamodb", select **DynamoDB**, select **Create table**
![Create DynamoDB table](https://user-images.githubusercontent.com/469847/224529867-c0ef7861-66d7-46a6-b7b2-b53bfb60e400.png)
2. In **Table name** fill in "serverless-todo-{{NICKNAME}}", my example is **serverless-todo-rioastamal**
3. In **Partition key** enter "pk" with type **String**
4. In the **Sort key** enter "sk" with type **String**
4. Leave the other options at their default values, then select the **Create table** button

Wait a minute then the table will be ready. It is marked with the status of the table, which is **Active**.

![DynamoDB table](https://user-images.githubusercontent.com/469847/222465947-8233779f-3f5c-421c-a3ba-1bcd58793f1a.png)

#### Sample data

In the created application, all items will be identified by a combination of `pk` and `sk`. The format of the data for the user is:

pk | sk
---|---
user#{{USERNAME}} | user

The data format for the Todo is:

pk | sk
---|---
todo#{{TODO_ID}} | todo#{{USERNAME}}

The following is an example of the items in the table.

pk | sk | data | created_at
---|----|------|-----------
user#rioastamal | user | `{ "username": "rioastamal", "password": "...", "fullname": "Rio Astamal", ... }` | 2023-02-22T00:32:43.255Z
todo#workshop123 | todo#rioastamal | `[{ "id": "todo-1", "title": "Create serverless workshop", "completed": false }]` | 2023-02-22T00:34:43.255Z

In DynamoDB the number of columns and the data type of each item does not have to be the same, except for the `pk` and `sk` columns, which must always be there because they are partition keys and sort keys.
  </details>
  <!-- /Create DynamoDB Tables -->

  <details>
    <summary><h3>Creating an Identity in Amazon SES</h3></summary>

To send email using Amazon Simple Email Service (Amazon SES), an identity is required. This identity is used when sending emails. It could be domain, subdomain, or email verification.

When the account is still in the Sandbox, we also need to enter the recipient's address into the verified identity.

In this step, we will create two verified identity emails, one for the sender and one for the recipient. We'll make use of the plus sign *+** on the email address to create an alias.

#### Create Identity for Sender

1. Go to [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage) page, select **Create identity** (or Select the **Configuration** menu on the left, select **Verified identities**, select **Create identity**)
![Create SES Identity](https://user-images.githubusercontent.com/469847/224530183-fc02c8d0-4027-4929-bae7-9914414a5235.png)
2. In **Identity type** select **Email address**
3. In **Email address**, enter "{{YOUR_EMAIL}}+sender@example.com", an example is **john+sender@gmail.com**
4. Select **Create identity**

You will receive a verification email from Amazon SES. Click the verification link to validate the identity of the sender's email.

#### Create an Identity for the Recipient

1. Go to [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage) page, select **Create identity** (or Select the **Configuration** menu on the left, select **Verified identities**, select **Create identity**)
2. In **Identity type** select **Email address**
3. In **Email address**, enter "{{YOUR_EMAIL}}+receiver@example.com", an example is **john+receiver@gmail.com**
4. Leave the other fields as default, select **Create identity**

Check your email for the verification link. After the verification process is complete, you should have two verified identities from one email address.

![Amazon SES verified identity](https://user-images.githubusercontent.com/469847/224512646-dd4c033c-8fd9-42d0-99f2-d26d3251b21d.png)
  
  </details>
  <!-- /Creating an Identity in Amazon SES -->

  <details>
    <summary><h3>Creating Parameters in AWS Systems Manager</h3></summary>

The API uses JWT for the authentication process. In generating the JWT token, a _secret_ value is required for the encryption process. We can place this _Secret_ in an environment variable, but a safer and preferable way is to store and encrypt its value in a separate place. Thus, use the AWS Systems Manager Parameter Store.

1. Go to [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home) page
2. On the **Application Management** menu, select **Parameter Store** then **Create parameter**
![Menu Parameter Store](https://user-images.githubusercontent.com/469847/224530635-5a4ee600-4374-4d93-9383-99b6ea9334dc.png)
3. In **Name** fill in &quot;/{{NICKNAME}}/serverless-todo/development/jwt-secret&quot; in this example, we use **/rioastamal/serverless-todo/development/jwt-secret**
4. On **Tier** select **Standard**
5. In **type** select **SecureString**, leave other options as default, in **Value** fill in &quot;_workshop-serverless-todo-123456_&quot;
6. Select **Create parameter**

> **IMPORTANT**: In production, the value of this jwt-secret should be a random character. Here, you can use OpenSSL result `openssl rand -base64 48`.

We will retrieve and use the parameter values in the Node.js code required by the API.

![Parameter Store Details](https://user-images.githubusercontent.com/469847/222470132-67498505-f17d-4a1a-9dba-5a6a278584f2.png)

  </details>
  <!-- /Creating Parameters in AWS Systems Manager -->

</details>
<hr>

<details>
  <summary><h2>Module 3 - Creating Backend</h2></summary>
  
  <details>
    <summary><h3>Create a Lambda Function</h3></summary>

We will create a function in AWS Lambda to run an application written in Node.js. The Node.js runtime is one of the official runtimes supported by Lambda.

We will integrate this function with Amazon API Gateway as a proxy/gateway so that we can access it from the internet.

1. In the _Search_ input in the AWS console, type &quot;lambda&quot; select **Lambda**, select **Create a function**
![Create a Lambda function](https://user-images.githubusercontent.com/469847/224604692-ceb202c9-e360-4ea8-b843-a54c58f58d4d.png)
2. Select **Author from scratch**
3. In **Function name** fill in "serverless-todo-api-{{NICKNAME}}", my example is **serverless-todo-api-rioastamal**
4. At **Runtime** select **Node.js 16.x** then **Architecture** select **x86_64**
5. Leave the rest according to the default value, then select **Create function**

Now a Lambda function has been created. We will try to run the function.

![Lambda Function](https://user-images.githubusercontent.com/469847/222830710-331677c7-838f-4860-b994-5c2016d8f7e9.png)

#### Create test events

A Lambda function is executed when a certain trigger event occurs. We will simulate the trigger of an event sent by the API Gateway.

1. On the detail page of the newly created Lambda function, select the **Test** tab then the **Test event** configuration will appear.
![Tab Test](https://user-images.githubusercontent.com/469847/222831701-07364e7d-e79f-4c3a-8f34-9f7dcc58613e.png)
2. In **Test event action** select **Create new event**
3. In **Event name**, fill in **api-gateway-proxy**
4. In **Event sharing settings** select **Private**
5. On **Template** select **API Gateway AWS Proxy**
6. select the **Save** button then **Test**

In the **Execution result** section, an output will appear as a JSON string from the Node.js code executed by Lambda.

![Execution result](https://user-images.githubusercontent.com/469847/222488291-0f8a6275-2d02-40a2-80c6-47e66fd66f40.png)

#### Updating Javascript Code

Go back to the **Code** tab and edit the Javascript `index.js` code to look like this.

```javascript
// exports.hanlder = async (event) => {
exports.main = async (event) => {
    // TODO implement
    const response = {
        statusCode: 200,
        // body: 'Hello from Lambda!',
        body: JSON.stringify(event, null, 2),
    };
    return response;
};
```

Save (**File** - **Save**) the code, then select **Deploy**, then select **Test**. The response you get should be an error.

```json
{
  "errorType": "Runtime.HandlerNotFound",
  "errorMessage": "index.handler is undefined or not exported",
  "trace": [
    "Runtime.HandlerNotFound: index.handler is undefined or not exported",
    "    at Object.UserFunction.js.module.exports.load (file:///var/runtime/index.mjs:1038:15)",
    "    at async start (file:///var/runtime/index.mjs:1200:23)",
    "    at async file:///var/runtime/index.mjs:1206:1"
  ]
}
```

That's because we configure the handler of the Lambda function with the value `index.handler`. This means that AWS Lambda will execute the `handler` method exported by the `index.js` file. We've changed the method from `exports.handler` to `exports.main` so that error occurs.

To fix this, change the handler configuration of this Lambda function.

![Runtime Settings](https://user-images.githubusercontent.com/469847/224609215-55383930-89be-40a9-8e7c-193fb8872f0a.png)

1. On the **Code** tab, scroll to the **Runtime settings** section then select **Edit**
2. In the **Handler** section change the value from `index.handler` to `index.main`
3. Select the **Save** button then select the **Test** button again

The function should now run normally and return output according to the content of the **api-gateway-proxy** test event on the `body` attribute.

![Execution test result API GW](https://user-images.githubusercontent.com/469847/222489811-e3f9b9d5-da1a-48e9-9f10-e7b65b2eb70c.png)

From this point, you start to understand the correlation between the Handler configuration in Lambda and how the exported file and method names should be named.

  </details>
  <!-- /Create a Lambda Function -->

  <details>
    <summary><h3>Connecting Lambda Functions to Amazon API Gateway</h3></summary>

Amazon API Gateway will act as a router directing requests to the created Lambda function.

1. Log in on the Amazon API Gateway page. In the input _Search_ AWS console then type "api gateway" - select **API Gateway**
2. In **Choose an API type** select **HTTP API** then select **Build**
![Create a HTTP API](https://user-images.githubusercontent.com/469847/224609729-610c23cd-644b-4745-a890-48ff907feb95.png)

The following configuration will link the created Lambda function with an HTTP API.

1. In **Integrations** select **Add Integration**, select **Lambda**, **AWS Region** select **ap-southeast-1**, **Lambda function** select the Lambda function has been created, **Version** select 2.0
2. In **API name**, fill in "serverless-todo-gw-{{NICKNAME}}", in this example, we use **serverless-todo-gw-rioastamal**, select **Next**
![Create API Gateway Step 1](https://user-images.githubusercontent.com/469847/224610349-940312cf-afdf-4ebc-86f6-5d75cc7e05be.png)
3. Then in the **Method** routing section select **ANY**, **Resource Path** replace the contents with **/{proxy+}**, **Integration target** select your Lambda function, in my case is **serverless-todo-api-rioastamal**
4. Select **Next**
![Create API Gateway Step 2](https://user-images.githubusercontent.com/469847/224611110-e17059b2-ca7e-486c-8658-a0843bde0be0.png)
5. In the stage configuration, on **Stage name** select **$default** and make sure **Auto-deploy** is active
6. On the review page, if it is appropriate, select **Create**
![Create API Gateway Review Step](https://user-images.githubusercontent.com/469847/224612157-2585f6a0-b846-4ada-a413-21143da2330d.png)

It will take you to the detail page of the HTTP API. Look at the Stage section where there is **Invoke URL** which is the address of the HTTP API.

![Invoke URL](https://user-images.githubusercontent.com/469847/222491008-ac4481ab-1604-4af8-a472-6c13d8d8416e.png)

Open the link to execute the newly created Lambda function. The output is a JSON string whose contents are requests sent by Amazon API Gateway. Mine the output is as follows.

```json
{
  "version": "2.0",
  "routeKey": "ANY /{proxy+}",
  "rawPath": "/",
  "rawQueryString": "",
  "headers": {
    "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8",
    "accept-encoding": "gzip, deflate, br",
    "accept-language": "en-US,en;q=0.5",
    "content-length": "0",
    "host": "syvyjs8mej.execute-api.ap-southeast-1.amazonaws.com",
    "sec-fetch-dest": "document",
    "sec-fetch-mode": "navigate",
    "sec-fetch-site": "cross-site",
    "sec-fetch-user": "?1",
    "upgrade-insecure-requests": "1",
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:102.0) Gecko/20100101 Firefox/102.0",
    "x-amzn-trace-id": "Root=1-63fdf4e7-1ea77c546d6226ed664d77a8",
    "x-forwarded-for": "180.253.89.124",
    "x-forwarded-port": "443",
    "x-forwarded-proto": "https"
  },
  "requestContext": {
    "accountId": "079418010844",
    "apiId": "syvyjs8mej",
    "domainName": "syvyjs8mej.execute-api.ap-southeast-1.amazonaws.com",
    "domainPrefix": "syvyjs8mej",
    "http": {
      "method": "GET",
      "path": "/",
      "protocol": "HTTP/1.1",
      "sourceIp": "180.253.89.124",
      "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:102.0) Gecko/20100101 Firefox/102.0"
    },
    "requestId": "BDM0Rg8AyQ0EMXA=",
    "routeKey": "ANY /{proxy+}",
    "stage": "$default",
    "time": "28/Feb/2023:12:34:47 +0000",
    "timeEpoch": 1677587687813
  },
  "pathParameters": {
    "proxy": ""
  },
  "isBase64Encoded": false
}
```

This application is still monolith, so the route path `/{proxy+}` functions as a catch-all route so that any path will be caught and forwarded to the specified target in this case, your Lambda function.

  </details>
  <!--Connecting Lambda Functions to Amazon API Gateway-->

  <details>
    <summary><h3>Running Todo API on AWS Cloud9</h3></summary>

Running the Todo API on AWS Cloud9 is the same as running it on a local machine. We will clone the Todo api project that has been prepared.

1. If your Cloud9 is not open yet, then enter on the [AWS Cloud9](https://console.aws.amazon.com/cloud9/home) page, select **Open** to open the pre-existing environment
2. Open a new terminal, make sure it's in `/home/ec2-user/environment`

```sh
cd ~/environment
```

3. Clone Serverless Todo API project

```sh
git clone https://github.com/rioastamal-examples/serverless-todo-express-api.git
```

4. Go to the project directory and install dependencies using `npm`

```sh
cd serverless-todo-express-api
```

```sh
npm install --omit=dev
```

To run the API we need to supply several environment variables, namely:
- `APP_TABLE_NAME`: DynamoDB table
- `APP_PARAMSTORE_JWT_SECRET_NAME`: Parameter Store name for jwt-secret
- `APP_FROM_EMAIL_ADDR`: the sender's email address that has been verified in Amazon SES

Back at the terminal on AWS Cloud9. Run this command to set environment variables in Terminal.

> **IMPORTANT**: Adjust the value of each of these environment variables to your own. You can copy code below to new file in Cloud9 first (no need to save) and then paste the code into Terminal.

```sh
export APP_TABLE_NAME=serverless-todo-{{NICKNAME}}
export APP_PARAMSTORE_JWT_SECRET_NAME=/{{NICKNAME}}/serverless-todo/development/jwt-secret
export APP_FROM_EMAIL_ADDR=EMAIL.SAYA+sender@gmail.com
```

Then run the `local.js` file.

```sh
node local.js
```

```
API server running on port 8080
```

> **IMPORTANT**: While testing the API running via `node local.js` if you get the error "_ExpiredTokenException: The security token included in the request is expired_" then go back to the terminal tab running `node local.js` press CTRL+C to stop the server and restart `node local.js`. By now the call API should not return the error.

Open a new terminal session on AWS Cloud9 by selecting the plus sign **+** then selecting **New Terminal**. In the new terminal, run the following command to test the API response on the `/protected` endpoint.

![Cloud9 open new terminal](https://user-images.githubusercontent.com/469847/224682926-a01b3508-85bd-4be2-88d1-c97026eb2581.png)

```sh
curl -s -D /dev/stderr http://localhost:8080/protected | jq .
```

```
HTTP/1.1 401 Unauthorized
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 26
ETag: W/"1a-pljHtlo127JYJR4E/RYOPb6ucbw"
Date: Tue, 28 Feb 2023 14:03:40 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Unauthorized"
}
```

The API should return HTTP status 401 which means authentication is required to access the endpoint.

The contents of the `local.js` file is similar to most scripts for running Node.js applications.

```javascript
const app = require('./src/index.js');

const port = process.env.APP_PORT || 8080;

app.listen(port, function() {
  console.log(`API server running on port ${port}`);
});
```

Where the application will bind to the default port `8080`. The `app` object is imported from the main file `src/index.js`. This file is not used when the application is run on AWS Lambda because the request/response format differs from the normal HTTP request.

#### Test POST /register Endpoint

Next let's try to do user registration. The endpoint used is `/register`. Make sure the email used is one that has been registered with a verified identity because Amazon SES status is still in the sandbox.

> **IMPORTANT**: Replace [RECIPIENT_EMAIL] with the recipient email verified in Amazon SES. You can copy the code below to a new file (without saving) in Cloud9 do new changes paste into Terminal.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
http://localhost:8080/register -d '
{
  "username": "workshop-user1",
  "password": "workshop123",
  "fullname": "User One",
  "email": "[RECIPIENT_EMAIL]"
}' | jq .
```

```
HTTP/1.1 201 Created
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 42
ETag: W/"2a-nMoFx54+czTntmSLXl3mqIsZV4A"
Date: Tue, 21 Feb 2023 15:45:00 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "User registered successfully"
}
```

Check the email to make sure the API has sent a welcome email. Email providers may classify email as spam due to the absence of several attributes, such as SPF and DKIM.

This is not a problem because we are only doing a test. So make sure to also check your spam/junk folder.

![Welcome email inbox](https://user-images.githubusercontent.com/469847/224705316-3c4d0fc3-df5d-4311-925d-baf2d964706d.png)

#### Test POST /login Endpoint

Now try to login to get JWT token.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
http://localhost:8080/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 232
ETag: W/"e8-MT+u0ta7SmxYtf5v5jjibWl/UnY"
Date: Tue, 21 Feb 2023 16:25:55 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "token": "SOME_LONG_JWT_TOKEN"
}
```

#### Test PUT /todos/:id Endpoint

Use the previously obtained token in the `Authorization` header. Run the command to create a new environment variable `JWT_TOKEN`.

> **IMPORTANT**: Replace the SOME_LONG_JWT_TOKEN value with the token value you got from the /login response

```sh
export JWT_TOKEN="SOME_LONG_JWT_TOKEN"
```

Now create a simple Todo list with the ID "{{NICKNAME}}-1", in this example, we are using **rioastamal-1**.

> **IMPORTANT**: You can first copy the command to the editor on Cloud9 then change {{NICKNAME}}-1 to your own nickname, then paste it in the Terminal.

```sh
curl -s -D /dev/stderr -XPUT \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/{{NICKNAME}}-1 -d '
[
  {
    "id": "todo-1",
    "title": "Workshop Serverless",
    "completed": false
  },
  {
    "id": "todo-2",
    "title": "Pulang makan",
    "completed": false
  }
]' | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 37
ETag: W/"25-XPFgY3+pqPIQgFjmpJbmM77Ikbo"
Date: Tue, 21 Feb 2023 16:40:44 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Todo successfully added"
}
```

#### Test GET /todos/:id Endpoint

Now try to get the Todo item that was just created. Customize with your own Todo ID and token.

> **IMPORTANT**: Replace {{NICKNAME}}-1 with your own entered from the previous command.

```sh
curl -s -D /dev/stderr \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/{{NICKNAME}}-1 | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 122
ETag: W/"7a-N/KgtPWXVbcHR0srdVVCfOmcjc0"
Date: Tue, 28 Feb 2023 16:42:40 GMT
Connection: keep-alive
Keep-Alive: timeout=5

[
  {
    "title": "Workshop Serverless",
    "id": "todo-1",
    "completed": false
  },
  {
    "title": "Pulang makan",
    "id": "todo-2",
    "completed": false
  }
]
```

  </details>
  <!--Running Todo API on AWS Cloud9-->
  
  <details>
    <summary><h3>View Data in DynamoDB</h3></summary>

We have performed several data write operations, namely when calling the `POST /register` and `PUT /todos/:id` endpoints. Maybe you want to see how the data is stored in the tables in DynamoDB.

1. Go to the DynamoDB console page.
2. Select **Explore Items** from the menu on the side, in **Tables** select the table you created, for example choose me **serverless-todo-rioastamal**
3. On **Scan or query items**, select **Scan**
4. Select **Run** to display all items in the table
![Scan Items DynamoDB](https://user-images.githubusercontent.com/469847/224723567-43cbd31e-ec7c-404c-a872-e475f14a25d2.png)

It will display results in the **Items returned** section. In the example, there should be two items, a data user and a Todo. Select the created Todo item to view the details of that item.

![Item on DynamoDB](https://user-images.githubusercontent.com/469847/224724397-5548394b-555c-4fc8-a628-e7f1dda2c916.png)

It will take you to the detail page of the item which is a JSON. Feel free to edit the item. However, in this example select **Cancel** to close the item detail page again.

> **IMPORTANT**: In production the **Scan** command is not recommended because it will read the entire table. It doesn't matter if the amount of data is small, but if the amount of data is large it will affect processing and costs. Always use **[Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)** when possible.
  </details>

  <details>
    <summary><h3>Deploy Code to AWS Lambda</h3></summary>

There are two principal ways to upload code to AWS Lambda. The first is directly from your local machine or from the S3 Bucket. We will use the second method. Uploaded files are in zip format.

The handler that this Lambda function will use is the `main` method in the `lambda.js` file.

Make sure you are in the root directory of the serverless-todo-express-api project. We will package the API code in a zip.

Make sure you are in the serverless-todo-api project directory if you aren't already.

```sh
cd ~/environment/serverless-todo-express-api/
```

Run the following command to upload the code to the S3 Bucket. My bucket name is **serverless-workshop-202303-rioastamal**, customize your own.

> IMPORTANT: Replace [BUCKET_NAME] with the S3 bucket you created earlier

```sh
export APP_FUNCTION_BUCKET=[BUCKET_NAME]
```

Then run the `build.sh` file

```sh
bash build.sh
```

```
Downloading app dependencies...

added 156 packages, and audited 157 packages in 3s

8 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Creating zip...
Uploading zip to S3 Bucket...
upload: .build/tmp/serverless-todo-api.zip to s3://serverless-workshop-202303-rioastamal/serverless-todo-api.zip
```

> NOTES: Files and directories packaged in the zip are <br>- `src/`<br>- `node_modules/`<br>- `lambda.js`

When finished, there should be a zip file named **serverless-todo-api.zip** in the bucket.

1. Go to [Amazon S3](https://s3.console.aws.amazon.com/s3/get-started) page
2. Select **Buckets** from the menu, select the created bucket.
3. Select the **serverless-todo-api.zip** file
4. On the **Properties** tab, copy the value in **Object URL**

![Copy Object URL](https://user-images.githubusercontent.com/469847/222493102-1eec90a9-a6db-4ed8-9ec7-4f72883a9e49.png)

Next, we'll upload the zip of the bucket into the Lambda function.

![Upload from](https://user-images.githubusercontent.com/469847/222837918-39f71233-9902-4239-b9a5-b7173fc7d8e8.png)

1. Enter the [AWS Lambda](https://console.aws.amazon.com/lambda/home) console
2. Select **Functions** select the function that has been created
3. On the **Code** tab, select **Upload from**, select **Amazon S3 location**
4. In **Amazon S3 link URL** fill in the value of the Object URL that was copied earlier

Next, enter the value to the environment variable.

1. Enter the Lambda function page that has been created
2. Select the **Configuration** tab, select **Environment variables**, select **Edit**
3. Select **Add environment variable**, enter the value according to yours:
    - Key: `APP_TABLE_NAME`, Value: `serverless-todo-{{NICKNAME}}`
    - Key: `APP_PARAMSTORE_JWT_SECRET_NAME`, Value: `/{{NICKNAME}}/serverless-todo/development/jwt-secret`
    - Key: `APP_FROM_EMAIL_ADDR`, Value: `YOUR.EMAIL+sender@gmail.com`
4. Select **Save**

![Environment variable](https://user-images.githubusercontent.com/469847/224726513-6ca251d2-dd19-476e-bd68-4886a398f193.png)

#### Test API via API Gateway

We will test whether the API runs normally when running on AWS Lambda.

1. Enter on [API Gateway](https://console.aws.amazon.com/apigateway/home) page
2. Select the API that has been created, for example choose me **serverless-todo-gw-rioastamal**
3. Copy the URL value in **Invoke URL**

> **IMPORTANT**: Don't forget to always replace the URL shown in the prompt with your own API Gateway URL

Back at the terminal on AWS Cloud9. Run the following command to try the API. Replace the URL `{{INVOKE_URL}}` with your own.

```sh
export INVOKE_URL={{INVOKE_URL}}
```

```sh
curl -s -D /dev/stderr $INVOKE_URL/protected | jq .
```

```
HTTP/2 500 
date: Tue, 21 Feb 2023 04:13:29 GMT
content-type: application/json
content-length: 35
apigw-requestid: BFWUKhzKSQ0EPVw=

{
  "message": "Internal Server Error"
}
```

Oops, why is that? Still remember the correlation between the Handler in the Runtime settings in Lambda and the Javascript filename?

Last time we changed its value to `index.main` which means Lambda will try to find the `index.js` file and call the `main` method. Whereas our current application uses the `lambda.js` file and the method that needs to be called is `handler`.

> **IMPORTANT**: Always remember the relationship between the Handler in the Lambda function and its correlation with the filename and method name being exported.

Yes, we have to change the Runtime Lambda configuration.

1. Enter the Lambda function page that has been created.
2. On the **Code** tab, scroll to the **Runtime settings** section and select **Edit**
3. In **Handler** fill in **lambda.handler**
4. Select **Save**

This is the contents of the `lambda.js` file

```js
const app = require('./src/index.js');
const serverless = require('serverless-http');

exports.handler = serverless(app);
```

In the code above, we import the `app` object from the main file `index.js`. The application does not bind to ports like it does in the `local.js` file. But only exporting a function on the `handler` attribute.

We make use of the [serverless-http](https://www.npmjs.com/package/serverless-http) library to convert the behavior from Lambda request/response to the normal HTTP request form understood by express.

Now on the terminal on AWS Cloud9 try to repeat the request that failed earlier. Should be able to now. Replace the URL with your own.

```sh
curl -s -D /dev/stderr $INVOKE_URL/protected | jq .
```

```
HTTP/2 401 
date: Tue, 21 Feb 2023 05:23:16 GMT
content-type: application/json; charset=utf-8
content-length: 26
etag: W/"1a-pljHtlo127JYJR4E/RYOPb6ucbw"
x-powered-by: Express
apigw-requestid: BFgifgkoSQ0EPhw=

{
  "message": "Unauthorized"
}
```

If we get 401 - Unauthorized then the API is responding correctly. Now try to login to the API.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/2 500 
date: Tue, 21 Feb 2023 05:26:34 GMT
content-type: application/json
content-length: 35
apigw-requestid: BFhBkioASQ0EPXQ=

{
  "message": "AccessDeniedException: User: arn:aws:sts::ACCOUNT_ID:assumed-role/serverless-todo-api-rioastamal-role-lgl95shr/serverless-todo-api-rioastamal is not authorized to perform: dynamodb:GetItem on resource: arn:aws:dynamodb:ap-southeast-1:ACCOUNT_ID:table/serverless-todo-rioastamal because no identity-based policy allows the dynamodb:GetItem action"
}
```

Ooops, what error is this again?

As you can see, we have a permissions problem, namely the Lambda function does not have permission to call the **dynamodb:GetItem** API. We will fix this problem.

#### Adding Permissions to Lambda Functions

The Node.js application that is made depends on several other AWS services such as Amazon DynamoDB, Amazon SES, and AWS Systems Manager. The recommended way to grant permissions is with the concept [_least-privilege_](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege).

This means that permissions or access rights are only given as needed, just enough for the function to run. However, in this workshop, we will provide slightly widened permissions to make it easier to understand.

Steps to add permissions to the Lambda function.

1. Go to the created Lambda function page.
2. Select the **Configuration** tab, select **Permissions**
3. In the **Execution role** section, there is a **Role name** used by our Lambda function.
4. Select the role, for example **serverless-todo-api-rioastamal-role-pkqnzkp3**
![Execution role](https://user-images.githubusercontent.com/469847/224752239-a20c0757-9dd3-4152-ad64-a1dde9a92f84.png)

You will be taken to the IAM page to edit the role. Make sure you are on the **Summary** page of this role.

1. On the **Permissions** tab select **Add permissions**
![Function roles](https://user-images.githubusercontent.com/469847/224753234-d42de7bc-5b93-49c8-bb7d-c3ef972e1dfd.png)
2. Select **Attach policies**
3. In **Other permissions policies** type **dynamodb** then ENTER
4. Check **AmazonDynamoDBFullAccess**, select **Clear filters**
5. In **Other permissions policies** type **ses** then ENTER
6. Check **AmazonSESFullAccess**, select **Clear filters**
7. In **Other permissions policies** type **ssm** then ENTER
8. Check **AmazonSSMReadOnlyAccess**, select **Clear filters**
9. Select **Add permissions**

![Permissions](https://user-images.githubusercontent.com/469847/222842504-8073651e-d387-4ad7-88c6-471ee26ebace.png)

Let's hit the `/login` endpoint that failed earlier.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/2 200 
date: Tue, 21 Feb 2023 06:16:24 GMT
content-type: application/json; charset=utf-8
content-length: 232
x-powered-by: Express
etag: W/"e8-CccXfxVNNNmhFbddbNAfsvGxLxI"
apigw-requestid: BFoUvjRoyQ0EMBg=

{
  "token": "SOME_LONG_JWT_TOKEN"
}
```

The process was successful and returned the JWT token. Now let's try new user registration.

> **IMPORTANT**: Replace [RECIPIENT_EMAIL] with your own. Make sure the recipient's email is the one that was previously verified in Amazon SES.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/register -d '
{
  "username": "workshop-user2",
  "password": "workshop123",
  "fullname": "User Two",
  "email": "[RECIPIENT_EMAIL]"
}' | jq .
```

```
HTTP/2 201 
date: Tue, 21 Feb 2023 06:21:20 GMT
content-type: application/json; charset=utf-8
content-length: 42
etag: W/"2a-nMoFx54+czTntmSLXl3mqIsZV4A"
x-powered-by: Express
apigw-requestid: BFpC_jblyQ0EMqg=

{
  "message": "User registered successfully"
}
```

If the email is received, then all permissions are correct.

  </details>
  <!--Deploy Code to AWS Lambda-->

</details>
<hr>

<details>
  <summary><h2>Module 4 - Creating a Frontend</h2></summary>

  <details>
    <summary><h3>Fork Repository serverless-todo-vue on GitHub</h3></summary>

In implementing the CI/CD frontend on AWS Amplify, you will later use the forked repository from serverless-todo-vue.

1. Make sure you are logged in to your GitHub Account.
2. Open the [serverless-todo-vue](https://github.com/rioastamal-examples/serverless-todo-vue) repo
3. Select **Fork** on the top right side

![Fork button](https://user-images.githubusercontent.com/469847/222701103-77ae5f47-bdf8-4119-af09-f02888f8db8b.png)

4. Leave the other options at their default values, Select **Create fork**

![Create Fork](https://user-images.githubusercontent.com/469847/222701481-fbb50246-ebe4-42fd-940a-b8c8dc142a67.png)

  </details>
  <!--Fork Repository serverless-todo-vue on GitHub-->

  <details>
    <summary><h3>Frontend Hosting on AWS Amplify</h3></summary>

The frontend application is built using HTML/Javascript/Vue.js. It consists of three files namely `index.html`, `login.htl`, and `register.html`. Application modified from [Vue.js TodoMVC](https://vuejs.org/examples/#todomvc) example.

One of the main features of AWS Amplify is static website hosting and automatic CI/CD integration with version control services such as AWS CodeCommit or GitHub.

In addition, the application will be placed on the Content Delivery Network (CDN) closest to the user automatically so that access can be faster.

In this workshop we will use GitHub as a place to store our frontend code repository. Make sure you have a GitHub account to follow this process.

#### Build an App on Amplify Hosting

Make sure you have forked the serverless-vue-todo repository.

1. Go to [AWS Amplify](https://console.aws.amazon.com/amplify/home) page
2. Select **GET STARTED**
![Amplify Get Started](https://user-images.githubusercontent.com/469847/224765306-67f1931f-c120-45f2-9a42-aebbd5462905.png)
3. In the Amplify Hosting section select **Get started**
![Web App Get Started](https://user-images.githubusercontent.com/469847/224765332-6c745d3f-e6c6-457c-9fd5-3fa9a97a8a36.png)
4. Select **GitHub**, select **Continue**

Amplify Hosting requires read access to the repository you want to integrate. A dialog will appear that AWS Amplify requires permission to access the repository.

1. Select **Authorize AWS Amplify (ap-southeast-1)**
2. (If the repository doesn't appear) Select **View GitHub permissions**, select the GitHub account where the repository is located, select **All repositories**, select **Install & Authorize**
3. On the Add repository branch page, in Recently updated repositories select **serverless-todo-vue** repository
4. On **Branch** select **main**, select **Next**
![Add GitHub Repository](https://user-images.githubusercontent.com/469847/224766571-3dfc8144-cf18-4338-943e-36b06360902f.png)

Next will appear the Build Settings page where you can configure how the application is built.

1. In **App name**, fill in "serverless-todo-vue-{{NICKNAME}}", my example is **serverless-todo-vue-rioastamal**
2. In **Build and test settings** select **Edit** fill in the following build code.

```yaml
version: 1
frontend:
  phases:
    build:
      commands: 
        - bash build.sh
  artifacts:
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths: []
```

3. Open **Advanced settings**, leave blank on **Build image**
4. Enter the following environment variables.
     - Key: `API_BASE_URL`, Value: `[HTTP_API_INVOKE_URL]` (make sure to replace with your HTTP API address in API Gateway, without the trailing slash "/")
5. Select **Next**, select **Save and deploy**

Wait for the build process to finish marked with green _Provision_, _Build_, and _Deploy_ indicators. You can select one of them to view detailed logs of each.

![Amplify Build Process](https://user-images.githubusercontent.com/469847/224769089-e05f9a2f-0cd6-403f-b269-a69d99d01944.png)

The build process in `build.sh` is very simple as it only demonstrates what Amplify Hosting can run when building the app.

```sh
#!/bin/sh

[ -z "$API_BASE_URL" ] && {
    echo "Missing API_BASE_URL env." 2>&1
    exit 1
}

mkdir -p build/

echo "Frontend Build: Replacing API_BASE_URL with ${API_BASE_URL}..."
for _file in index.html login.html register.html
do
    sed "s#{{API_BASE_URL}}#$API_BASE_URL#g" $_file > build/$_file
done
```

This script simply rewrites the string `{{API_BASE_URL}}` with the value supplied from the environment variable. Then copy the HTML files of the `build/` folder.

  </details>
  <!--Frontend Hosting on AWS Amplify-->

  <details>
    <summary><h3>Test Frontend Todo App</h3></summary>

When the build process is complete, select **main** to see the details of the last build process. Then select the URL link on **Domain** to open the frontend application.

![Amplify Link](https://user-images.githubusercontent.com/469847/224769818-55d45171-500c-4b9f-92bd-acfc77fb7975.png)

![Login page image](https://user-images.githubusercontent.com/469847/222702635-1635d84f-0a09-4f50-9277-0ded19a4e5ea.png)

The application will redirect to `/login.html` page if the user has not authenticated. Before trying to login, first activate the debugger tools in your web browser and open the Network tab.

Use **username** "workshop-user1" and **password** "workshop123".

![Login error](https://user-images.githubusercontent.com/469847/222702717-72d9cb31-49fc-49bc-8999-6e049b11c475.png)

What should have happened is that the authentication process failed. If you open the Debugger tools in a web browser, an error message appears due to a CORS problem.

#### Overcoming CORS via Amazon API Gateway

There are two ways to solve this CORS problem, you change the source code of the API to add CORS HTTP headers or via Amazon API Gateway. We will use the second method.

1. Go to [Amazon API Gateway](https://console.aws.amazon.com/apigateway/main/) page
2. Select the pre-built API, mine is **serverless-todo-gw-rioastamal**
3. On the **Develop** menu on the left, select **CORS**, select **Configure**
4. In each **Access-Control-Allow-Origin** and **Access-Control-Allow-Headers** fill in &quot;\*&quot; then select **Add**
5. In **Access-Control-Allow-Methods** select **\***
6. Select **Save**

![CORS Configuration](https://user-images.githubusercontent.com/469847/222702848-3b78f73c-ea91-4f31-8b95-94ed115234c7.png)

Return to the `/login.html` page and try the process again. You should now be able to log in.

#### Trying to Create a New Todo

You should now be on the `/index.html` page. If not then open the page. When you first access this Todo list, the application will automatically try to create an empty Todo with a random ID.

![Random Id](https://user-images.githubusercontent.com/469847/222704021-99f441bc-3a40-402e-800d-59d4d4f922ab.png)

Do you still remember the step [Trying Endpoint PUT /todos/:id](#) you created Todo via CLI on AWS Cloud9? We will try to update it. In this example I create a Todo with ID **rioastamal-1**.

1. In the input **Create or update todos** enter **[TODO ID]** (change according to the Todo ID you entered in step [trying PUT endpoint /todos/:id](#trying-endpoint-put-todosid ), then press ENTER
2. Two Todo items will appear, namely &quot;Serverless Workshop&quot; and "Come home for lunch", fill in the new Todo item "Tidur" press ENTER

![Old Todo Id](https://user-images.githubusercontent.com/469847/222704718-ee8cdc08-8750-457c-896d-4e47ce50cefd.png)

![Todo Tidur](https://user-images.githubusercontent.com/469847/222705816-570a7e3c-a241-4e6a-a622-04c3c6f0f0f2.png)

You can go to the previously created [DynamoDB Table](https://console.aws.amazon.com/dynamodbv2/home/#table) page to see the data that has been entered.
### Adding Links between Pages

To try the CI/CD implementation at Amplify Hosting, we will slightly change the appearance of the page by adding a link at the bottom.

First, we will clone the serverless-todo-vue project from each of your GitHub accounts.

> **IMPORTANT**: Clone from your fork repo, not from the original rioastamal-examples/serverless-todo-vue repo.

1. Go to the serverless-todo-vue project page that you forked on GitHub
2. Select **Code**, in the **Clone** option on the **Local** tab, make sure **SSH** is selected
3. Copy the SSH version of the repository URL

![Clone from fork](https://user-images.githubusercontent.com/469847/222708678-bfa3b90f-bdce-4389-82a1-9f2f5028762d.png)

Log in on a terminal on AWS Cloud9. Make sure you are in the `~/environment` directory.

```sh
cd ~/environment
```

Use the following `git clone` command to clone the project. Customize it with your own repo address.

```sh
git clone git@github.com:[YOUR_GITHUB_ACCOUNT]/serverless-todo-vue.git
```

```
Cloning into 'serverless-todo-vue'...
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
ECDSA key fingerprint is MD5:7b:99:81:1e:4c:91:a5:0d:5a:2e:2e:80:13:3f:24:ca.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,20.205.243.166' (ECDSA) to the list of known hosts.
remote: Enumerating objects: 33, done.
remote: Counting objects: 100% (33/33), done.
remote: Compressing objects: 100% (20/20), done.
remote: Total 33 (delta 14), reused 28 (delta 9), pack-reused 0
Receiving objects: 100% (33/33), 8.33 KiB | 8.33 MiB/s, done.
Resolving deltas: 100% (14/14), done.
```

#### File login.html

Open the `login.html` file on Cloud9 and add a link to the `/register.html` page by uncomment code around line 30.

![Edit File login.html](https://user-images.githubusercontent.com/469847/224772352-15bb598d-e211-4f66-b1b7-891d109655e2.png)

```html
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
```

To be like the following.

```html
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
```

#### File register.html

Open the `register.html` file on Cloud9 and add a link to the `/login.html` page by uncomment code around line 48.

```html
<!-- BEGIN - Remove this line
<section class="main">
  <p><a href="login.html">Login</a></p>
</section>
END - Remove this line -->
```

To be like the following.

```html
<section class="main">
  <p><a href="login.html">Login</a></p>
</section>
```

#### File index.html

Open the `index.html` file and add a link to the `/login.html?logout` page uncomment code around line 300.

```html
<!-- BEGIN - Remove this line
<p><a href="login.html?logout">Logout</a></p>
END - Remove this line -->
```

To be like the following.

```html
<p><a href="login.html?logout">Logout</a></p>
```

#### Commit Changes

Make sure you are in the project root directory of `serverless-todo-vue`.

```sh
cd ~/environment/serverless-todo-vue/
```

There should now be three files modified and ready to commit.

```sh
git status
```

```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html
        modified:   login.html
        modified:   register.html

no changes added to commit (use "git add" and/or "git commit -a")
```

Do a `git commit` to save changes.

```sh
git add index.html login.html register.html
```

Next.

```sh
git commit -m "Add links at the bottom of each page"
```

Push changes to GitHub repository.

```sh
git push origin main
```

When the push is successful now try to go to [AWS Amplify](https://console.aws.amazon.com/amplify/home) page. The build process should be running.

![Amplify Build](https://user-images.githubusercontent.com/469847/224782010-8534391a-b03d-4b36-a8ff-f773e57c4473.png)

This indicates AWS Amplify detects changes and builds automatically. With this you don't need to manage your own CI/CD server for the frontend.

When the build is complete try to open the Frontend application page again to make sure that the changes have been deployed perfectly. This is indicated by a link at the bottom of each page.

![New HTML page deployed](https://user-images.githubusercontent.com/469847/224783017-0ebbd4af-1e9f-42d1-bfb3-a1e3ba47fbc7.png)

  </details>
  <!--Test Frontend Todo App-->

</details>
<hr>

<details>
  <summary><h2>Module 5 - Decoupling</h2></summary>
  
  <details>
    <summary><h3>Registration Process Decoupling</h3></summary>

If you notice, the registration process is still tied to another process that can actually be separated, namely the process of sending emails.

Imagine if the email service has a problem then the registration process is considered a failure by the end user. Even though the actual delivery process can wait a few moments after registration is complete.

To do decoupling, you can use a queue and the registration process will send a message to the queue. Furthermore, messages in the queue will be processed by the worker in charge of sending emails.

Workers for sending email will later be run on AWS Lambda and Amazon SQS to store queues.

We can easily integrate Amazon SQS with AWS Lambda. Where if there is a new message entering the queue in SQS, then the message can be forwarded to a Lambda function.

![serverless-workshop-decoupled](https://user-images.githubusercontent.com/469847/224513755-ab4be3e3-2cbb-4aae-8106-c93ce8a036b6.png)

> Decoupling of email sending from registration API

  </details>
  <!--Registration Process Decoupling-->

  <details>
    <summary><h3>Create SQS queues</h3></summary>

We'll be using Amazon SQS to perform API decoupling of tasks that don't have to finish right away. In this case the welcome email delivery task.

This task does not have to be completed immediately when the registration API is called and there is a possibility that the email sending process is slow. So this task is a suitable candidate to be sent to the queue.

1. Enter on [Amazon SQS](https://console.aws.amazon.com/sqs/home) page
2. Select **Create queue**
![Create SQS queue](https://user-images.githubusercontent.com/469847/224784309-83695da3-f0f3-4a45-812d-29625060fddd.png)
3. In the **Type** option, select **Standard** and in **Name** fill in "serverless-todo-welcome-email-{{NICKNAME}}", my example is **serverless-todo- welcome-email-rioastamal**
4. Leave the rest according to the default value, then select **Create queue**

Later this queue will be used to store messages sent by the API when the user has just registered.

Then this message will be processed by the worker in this case is a Lambda function.
  
  </details>
  <!--Create SQS queues-->

  <details>
    <summary><h3>Creating a Welcome Email Worker with AWS Lambda</h3></summary>

The Lambda function is responsible for processing messages in the queue sent by the registration process.

1. Go to the [AWS Lambda](https://console.aws.amazon.com/lambda/home) page. then **Functions** page, select **Create function**
2. Select **Author from scratch**
3. In **Function name** fill in "serverless-todo-email-worker-{{NICKNAME}}", my example is **serverless-todo-email-worker-rioastamal**
4. At **Runtime** select **Node.js 16.x** then **Architecture** select **x86_64**
5. In **Change default execution role**, select **Use an existing role**, in **Existing role** select the role that was previously created, my example is **service-role/serverles-todo-api- rioastamal-role-RANDOM**
6. Leave the rest according to the default value, then select **Create function**

![Existing role](https://user-images.githubusercontent.com/469847/222845182-9432c93b-1d6c-4121-85a5-d17d12f6ea1f.png)

We choose the previous role so we don't have to reset permissions. In the case of production you should only grant the necessary permissions.

Next we will configure the necessary environment variables.

1. In the created Lambda function, select the **Configuration** tab, select **Environment variables**
2. Select **Edit**, select **Add environment variable**, add the following environment variables but adjust them to yours.
    - **Key**: `APP_TABLE_NAME`, **Value**: `serverless-todo-{{NICKNAME}}`
    - **Key**: `APP_URL`, **Value**: `{{AMPLIFY_URL}}` (URL from Amplify Hosting for frontend application)
    - **Key**: `APP_FROM_EMAIL_ADDR`, **Value**: `YOUR.EMAIL+sender@gmail.com` (Replace with verified sender email in Amazon SES)
3. Select **Save**

This Lambda function needs to access Amazon SQS so permissions need to be added to the _execution role_.

1. Still on the **Configuration** tab, select **Permissions**
2. In **Execution Role** select **Role name** to open the IAM page

We will add permissions so that the Lambda function can access Amazon SQS.

3. Select **Add Permissions**, select **Attach policies**
4. In the search, enter "SQS" then ENTER
5. Check **AWSLambdaSQSQueueExecutionRole**
6. Select **Add permissions**

![New SQS permission](https://user-images.githubusercontent.com/469847/222846137-4ddd99e2-4fb5-4298-a6cd-2299a72f17b0.png)

The next step is the integration of the SQS queue with the Lambda function.

1. Still on the **Configuration** tab, select **Triggers**, select **Add trigger**
2. In **Trigger configuration** type &quot;SQS&quot; then select **SQS**
3. In **SQS queue** select the queue that was created before, mine is **serverless-todo-welcome-email-rioastamal**
![Add SQS Trigger](https://user-images.githubusercontent.com/469847/224788078-551e2d96-29cf-41b2-b1a5-12a13b6c6444.png)
4. Leave the rest as default, select **Add**

#### Clone Email Worker Repo

Next clone the repository [serverless-todo-email-worker](https://github.com/rioastamal-examples/serverless-todo-email-worker). Log in to a terminal session on AWS Cloud9 making sure you are in `~/environment`.

```sh
cd ~/environment
```

Then do a clone.

```sh
git clone git@github.com:rioastamal-examples/serverless-todo-email-worker.git
```

Go to the `serverless-todo-email-worker` directory and install all dependencies

```sh
cd serverless-todo-email-worker
```

```sh
npm install --omit=dev
```

The registration process will then send a message to the queue in JSON format as follows:

```json
{ 
  "username": "USERNAME"
}
```

The worker will then take the message and query DynamoDB to get the details of the username `USERNAME` such as `fullname` and `email`. After getting the data, the worker will send email using Amazon SES.

#### Test Local Worker

Unlike the code in `serverless-todo-api`, this worker is purely made specifically to run on Lambda so there are no port binding processes and so on. It can be seen in `src/handlers/main.js`.

To run locally you need to call the `local.js` file. This file will read the `event-sqs.sample.json` file which simulates events from SQS.

We will try to resend registration email to username `workshop-user1`. There are several environment variables that need to be set, namely:

- `APP_TABLE_NAME` table name in DynamoDB
- `APP_FROM_EMAIL_ADDR` a verified sender email in Amazon SES
- `APP_URL` frontend URL on Amplify Hosting
- `APP_DUMMY_SQS_BODY` simulated sent data (for local execution only)

Adjust the value to yours.
```sh
export APP_TABLE_NAME=serverless-todo-{{NICKNAME}} \
APP_URL={{AMPLIFY_URL}} \
APP_FROM_EMAIL_ADDR=YOUR.EMAIL+sender@gmail.com \
APP_DUMMY_SQS_BODY='{ "username": "workshop-user1" }'
```

```sh
node local.js
```

```
...
mailResponse {
  '$metadata': {
    httpStatusCode: 200,
    requestId: '336fa895-4124-4f52-b4bb-e23c42d6616f',
    extendedRequestId: undefined,
    cfId: undefined,
    attempts: 1,
    totalRetryDelay: 0
  },
  MessageId: '010e0186dc260d9c-402d537b-7d67-48bc-8763-3eebac2aa6e7-000000'
}
Email sent
```

If successful then you should receive an email. Check the email inbox used by `workshop-user1`.

In this Lambda worker email function we add some metadata to the contents of the email.

![Email from worker](https://user-images.githubusercontent.com/469847/224790566-728be554-82e0-454d-8f9d-5748a6eaf400.png)

#### Deploy Email Worker to Lambda Function

We will package the script into a zip and upload it to the S3 bucket, then we will import it into the AWS Lambda function that was created.

Make sure you are still in the root directory `serverless-todo-email-worker`. Run the `build.sh` script and set the value of the `APP_FUNCTION_BUCKET` environment variable to your own.

> **IMPORTANT**: Replace [BUCKET_NAME] with the S3 bucket you created earlier

```sh
export APP_FUNCTION_BUCKET=[BUCKET_NAME]
```

```sh
bash build.sh
```

Next we'll upload the zip of the bucket into the Lambda function.

1. Enter the [AWS Lambda](https://console.aws.amazon.com/lambda/home) console
2. Select **Functions** select the function that has been created
3. On the **Code** tab, select **Upload from**, select **Amazon S3 location**
4. In **Amazon S3 Link URL**, fill in the Object URL of **serverless-todo-email-worker.zip** that was just uploaded. You can see it via the Amazon S3 page.

#### Entering an Environment Variable in a Lambda Function

Next enter the value to the environment variable required by the API.

1. Still on the Lambda email worker function page
2. Select the **Configuration** tab, select **Environment variables**, select **Edit**
3. Select **Add environment variable**, enter the value according to yours:
    - Key: `APP_TABLE_NAME`, Value: `serverless-todo-{{NICKNAME}}`
    - Key: `APP_URL`, Value: `[Amplify Hosting URL]`
    - Key: `APP_FROM_EMAIL_ADDR`, Value: `YOUR.EMAIL+sender@gmail.com`
4. Select **Save**

Note that the main function of worker email is the file `src/handlers/main.js` and the function name is `welcomeEmailSender`. So you need to change the Handler configuration.

1. Still on the Lambda email worker function page
2. On the **Code** tab, **Runtime settings** section, select **Edit**
3. In **Handler**, enter **src/handlers/main.welcomeEmailSender**
4. Select **Save**

#### Test Email Worker via Test event

Go to the generated email worker Lambda function page, mine is **serverless-todo-email-worker-rioastamal**.

1. Select the **Test** tab
2. Scroll to **Even JSON**, enter the following JSON

```json
{
    "Records": [
        {
            "messageId": "059f36b4-87a3-44ab-83d2-661975830a7d",
            "receiptHandle": "AQEBwJnKyrHigUMZj6rYigCgxlaS3SLy0a...",
            "body": "{ \"username\": \"workshop-user1\" }",
            "attributes": {
                "ApproximateReceiveCount": "1",
                "SentTimestamp": "1545082649183",
                "SenderId": "AIDAIENQZJOLO23YVJ4VO",
                "ApproximateFirstReceiveTimestamp": "1545082649185"
            },
            "messageAttributes": {},
            "md5OfBody": "098f6bcd4621d373cade4e832627b4f6",
            "eventSource": "aws:sqs",
            "eventSourceARN": "arn:aws:sqs:us-east-2:123456789012:my-queue",
            "awsRegion": "ap-southeast-1"
        }
    ]
}
```

3. Select **Test** without the need to save
4. Select **Details** to see the output of the execution

If the words **Message sent to workshop-user1** appear, it means the process is running successfully. Check your email inbox, this email was sent to the username `workshop-user1` created in the previous steps.

  </details>
  <!--Creating a Welcome Email Worker with AWS Lambda-->

  <details>
    <summary><h3>Decoupling Email from API /register</h3></summary>

The decoupling process on the registration API side in the `serverless-todo-api` project is by removing the email sending process code and replace it with a queue.

In this workshop, we have prepared the finished code in a different branch, namely `decoupled-welcome-email`.

In a terminal on AWS Cloud9 use the following command to view remote branches in the `serverless-todo-express-api` project.

```sh
cd ~/environment/serverless-todo-express-api
```

```sh
git branch -a
```

```
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/decoupled-welcome-email
  remotes/origin/main
```

Create a new branch called `decoupled-welcome-email` and enter that branch.

```sh
git checkout -b decoupled-welcome-email origin/decoupled-welcome-email
```

There should now be two branches on local. The active branch is `decoupled-welcome-email` marked with a `*`.

```sh
git branch
```

```
* decoupled-welcome-email
  main
```

Open the file `src/index.js` on AWS Cloud9 again, it will be different from before. Where now the `sendWelcomeEmail` function does not send an email directly but only sends a message to the SQS queue.

```javascript
async function sendWelcomeEmail(username)
{
  const queueResponse = await sqsclient.send(new SendMessageCommand({
    QueueUrl: sqsQueueUrl,
    MessageBody: JSON.stringify(({ username: username }))
  }));
  
  console.log('queueResponse', queueResponse);
}
```

#### Re-deploy Lambda Functions for Backend

Make sure it is still in the `decoupled-welcome-email` branch. Run the command to build and upload the zip to the S3 bucket. Customize your own bucket name.

> **IMPORTANT**: Replace [BUCKET_NAME] with your previously created bucket.

```sh
export APP_FUNCTION_BUCKET=[BUCKET_NAME]
```

```
bash build.sh
```

After successful upload, continue to import the zip file.

1. Go to the [AWS Lambda](https://console.aws.amazon.com/lambda/home) page, select the function serverless-todo-api-{{NICKNAME}}
2. Select the **Code** tab, select **Upload from**, select **Amazon S3 location**
3. In **Amazon S3 link URL** fill in the value of the Object URL `serverless-todo-api.zip` which is in the S3 bucket. (Go to the S3 bucket page to copy the values)

Next is to add a new environment variable in the Lambda function. That is `APP_SQS_URL`, you can see the URL value on the SQS queue page that was created earlier.

1. Still on the same Lambda function page
2. Select the **Configuration** tab, select **Environment variables**
3. Select **Edit** and add the following environment variables.
    - **Key**: `APP_SQS_URL`, **Value**: `[SQS_URL]` (Replace [SQS_URL] with your own)
4. Select **Save**

![SQS URL](https://user-images.githubusercontent.com/469847/222715597-682bd709-ec0e-426a-a9c7-f953b93c6243.png)

Now the decoupling process for sending the welcome email and registration should be complete.

Reopen the frontend page.

1. Navigate to `/login.html?logout` to delete the token in local storage
2. Navigate to `/register.html` to try to register a user with a decoupling backend from the welcome email.

If all goes well, then the user data should be entered into DynamoDB and the email should also be sent.

<blockquote>
<b>Or an error occurred? If an error occurs then it's your job to fix it :).</b>

Instruction:
  
- Open the network debugger in a web browser or try to view the logs of the Lambda function for errors that occur
- Add required permissions
</blockquote>

  </details>
  <!--Decoupling Emails from the Backend-->
</details>
<hr>

<details>
  <summary><h2>Closing</h2></summary>

Congratulations you have completed the "Building Backend and Frontend with Serverless Architecture" workshop.

In this workshop you have learned how to leverage AWS services for backend and frontend application deployment. AWS Lambda for running code, Amazon DynamoDB as NoSQL database, AWS Amplify for frontend hosting, Amazon SES for sending email, Amazon API Gateway as proxy/router, Amazon SQS for storing queues and AWS Systems Manager for storing secret parameters.

With serverless you don't have to worry about server management. Serverless has the characteristics of auto-scaling, has high availability, and is scale-to-zero so that costs are lower.

This allows you to focus on the application and allows for faster releases and feedback.

### Clean Up

If you run this workshop using your own account then you need to delete the resources that have been created to avoid being charged a fee.

You can go to the respective service page to delete it. For example entering Amazon S3 and deleting the files in the bucket used in this workshop.

Here are some of the resources created in this workshop.

- S3 buckets
- DynamoDB tables
- Parameter Store in AWS Systems Manager
- Lambda functions
- Verified identity on Amazon SES
- SQS queues
- Apps on AWS Amplify
- SSH Public key on GitHub

</details>