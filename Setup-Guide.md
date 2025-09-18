Step1.
First we should have the AWS account created or with IAM User you can login
=
<img width="1920" height="949" alt="Screenshot (11)" src="https://github.com/user-attachments/assets/dfe93707-7e5e-41b6-ab3d-b2fb8dc86903" />


Step2. Create a Lambda Function
=

**1.** Go to AWS Console → **Lambda** → **Create function**.

**2.** Choose:

   **Author from scratch**

   Function name: Lambda-canary-Project

   Runtime: Python 3.x or Node.js 18


**3.** Add sample code (example in Python):

import json

        def lambda_handler(event, context):
           # TODO implement
                return {
                   'statusCode': 200,
                   'body': json.dumps('This msessage come from Development server')
                      }

**4.** Click **Deploy**.
<img width="1901" height="912" alt="image" src="https://github.com/user-attachments/assets/8cd81721-15b1-4ca3-8670-78d4261f97a6" />
<img width="1878" height="905" alt="image" src="https://github.com/user-attachments/assets/0cd23f01-5f80-496b-a7dd-733b27da45aa" />
<img width="1880" height="912" alt="image" src="https://github.com/user-attachments/assets/e0a7e789-1a27-47cf-92ec-b9c7db35f10d" />





**Step2. Create an API Gateway**
=

**1.** Go to **Amazon API Gateway** → **Create API** →**REST API**.

**2.** Configure:

      API Name: API-Canary-Project

    Integration: Select Lambda Function → Lambda-canary-Project.
    
<img width="1865" height="912" alt="image" src="https://github.com/user-attachments/assets/c5fb1dc0-4eab-4fc9-8721-068f4ea39979" />

**3.** Click Deploy → Choose Stage: prod.

You now have an endpoint like:

       https://<api-id>.execute-api.<region>.amazonaws.com/prod/
<img width="1881" height="918" alt="image" src="https://github.com/user-attachments/assets/bd65e3eb-b943-4939-afd1-a14b71208b2a" />
<img width="1882" height="912" alt="image" src="https://github.com/user-attachments/assets/ed710762-0eca-42e2-92ce-daead749fca1" />
**4.**Output:
<img width="1905" height="248" alt="image" src="https://github.com/user-attachments/assets/34f02600-7166-4ffc-97f4-7ae7f7264dec" />




Step3. Enable Lambda Versions & Aliases
=

**1.** Go to Lambda → Lambda-canary-Project.

**2.** Click Publish new version → Name it v1.

**3.** Create an Alias (e.g., prod) pointing to version v1.


Step 4. Canary Deployment Setup
=

A Canary deployment allows you to gradually shift traffic from one Lambda version to another.

**1.** In Lambda → Aliases → prod.

**2.** Click Edit traffic shifting.

**3.** Choose:

     Additional version: v2 (publish new version after code update).

     Traffic shifting: e.g., 10% to v2, 90% stays on v1.

**4.** Save changes.

Now requests hitting API Gateway → Lambda prod alias will be split between v1 and v2.

<img width="1882" height="915" alt="image" src="https://github.com/user-attachments/assets/7e06da51-4f8d-4e04-b203-6e3cea520672" />



Step 5. Test the Setup
=

**1.** Update Lambda with new code, publish as v2.

   import json

       def lambda_handler(event, context):
          # TODO implement
             return {
                'statusCode': 200,
                    'body': json.dumps('This msessage come from Production server')
                    }


**2.** Invoke API Gateway endpoint:

           curl https://<api-id>.execute-api.<region>.amazonaws.com/prod/


Some responses will show v1.
<img width="1905" height="249" alt="Screenshot 2025-09-18 122613" src="https://github.com/user-attachments/assets/aed63ac0-4106-420f-8e09-353c810e80dc" />

Some will show v2 (based on canary %).
<img width="1905" height="927" alt="image" src="https://github.com/user-attachments/assets/b5f409b4-3a31-4238-a84f-58c1c4a3bf18" />

