# **AWS Game Day Notifications – Love Island Style** 🏀💌

## **Project Overview**
Ever wondered how AWS manages automated alerts? Picture **AWS services as Love Island production staff**, working tirelessly behind the scenes to keep you updated on the drama (or in this case, NBA scores).  

This project sends **real-time NBA game day score notifications** via **SMS and Email** to subscribed users using **Amazon SNS, AWS Lambda, and EventBridge**.  

Here’s the lineup:  
- **EventBridge** = The **producers** scheduling the updates & dropping bombshells 🎬  
- **Lambda** = The **video editors**, cutting out the fluff & keeping the juicy moments 🎞️  
- **SNS** = The **ITV broadcast team**, ensuring the audience gets the highlights 📢  

---

## **How It Works (The Love Island Pipeline)**
1️⃣ **EventBridge** (The Love Island Producers) 📅  
- Decides when updates should go live – either **scheduled (9 PM show)** or if **unexpected drama happens (bombshell alert)**.  
- **Doesn’t actually narrate anything**—it just kicks things off.  

2️⃣ **Lambda** (The Groggy Video Editors) 🎥  
- Wakes up to **watch the hidden cameras (API requests)**.  
- Decides what’s important: fights, game scores, recoupling moments.  
- **Formats the updates so they’re short & spicy**.  

3️⃣ **SNS** (The ITV Broadcast & Social Media Team) 📲  
- Takes the **final edited version** and delivers it **straight to your inbox/SMS**.  
- **Iain Stirling = SNS**—he doesn’t watch live but narrates what’s fed to him.
---
## **Setup Instructions**

### **Clone the Repository**

### **Create an SNS Topic**
1. Open the AWS Management Console.
2. Navigate to the SNS service.
3. Click Create Topic and select Standard as the topic type.
4. Name the topic (e.g., gd_topic) and note the ARN.
5. Click Create Topic.

### **Add Subscriptions to the SNS Topic**
1. After creating the topic, click on the topic name from the list.
2. Navigate to the Subscriptions tab and click Create subscription.
3. Select a Protocol:
- For Email:
  - Choose Email.
  - Enter a valid email address.
- For SMS (phone number):
  - Choose SMS.
  - Enter a valid phone number in international format (e.g., +1234567890).

4. Click Create Subscription.
5. If you added an Email subscription:
- Check the inbox of the provided email address.
- Confirm the subscription by clicking the confirmation link in the email.
6. For SMS, the subscription will be immediately active after creation.

### **Create the SNS Publish Policy**
1. Open the IAM service in the AWS Management Console.
2. Navigate to Policies → Create Policy.
3. Click JSON and paste the JSON policy from gd_sns_policy.json file
4. Replace REGION and ACCOUNT_ID with your AWS region and account ID.
5. Click Next: Tags (you can skip adding tags).
6. Click Next: Review.
7. Enter a name for the policy (e.g., gd_sns_policy).
8. Review and click Create Policy.

### **Create an IAM Role for Lambda**
1. Open the IAM service in the AWS Management Console.
2. Click Roles → Create Role.
3. Select AWS Service and choose Lambda.
4. Attach the following policies:
- SNS Publish Policy (gd_sns_policy) (created in the previous step).
- Lambda Basic Execution Role (AWSLambdaBasicExecutionRole) (an AWS managed policy).
5. Click Next: Tags (you can skip adding tags).
6. Click Next: Review.
7. Enter a name for the role (e.g., gd_role).
8. Review and click Create Role.
9. Copy and save the ARN of the role for use in the Lambda function.

### **Deploy the Lambda Function**
1. Open the AWS Management Console and navigate to the Lambda service.
2. Click Create Function.
3. Select Author from Scratch.
4. Enter a function name (e.g., gd_notifications).
5. Choose Python 3.x as the runtime.
6. Assign the IAM role created earlier (gd_role) to the function.
7. Under the Function Code section:
- Copy the content of the src/gd_notifications.py file from the repository.
- Paste it into the inline code editor.
8. Under the Environment Variables section, add the following:
- NBA_API_KEY: your NBA API key.
- SNS_TOPIC_ARN: the ARN of the SNS topic created earlier.
9. Click Create Function.


### **Set Up Automation with Eventbridge**
1. Navigate to the Eventbridge service in the AWS Management Console.
2. Go to Rules → Create Rule.
3. Select Event Source: Schedule.
4. Set the cron schedule for when you want updates (e.g., hourly).
5. Under Targets, select the Lambda function (gd_notifications) and save the rule.


### **Test the System**
1. Open the Lambda function in the AWS Management Console.
2. Create a test event to simulate execution.
3. Run the function and check CloudWatch Logs for errors.
4. Verify that SMS notifications are sent to the subscribed users.

---
## **Debugging Issues Found **
### **Time Zone Chaos: My Debugging Heartbreak 💔**  
- **Lambda doesn’t care about my local timezone**—it runs in UTC by default.  
- The first time I tested my function, **my alerts were off by HOURS**, like a Love Island text sent at the wrong moment.  
- Fix? **Installed `pytz` for timezone handling**, but AWS Lambda wasn’t having it.  
- Ended up **removing `pytz` and manually setting UTC offsets**, because sometimes **you gotta work with what you have.**

 My **first SNS email got unsubscribed out of nowhere**, and I had to **switch to JSON-type notifications**.  
- When I received the email, instead of a normal message, **I got a massive block of JSON data** with an unsubscribe token buried inside it.  
- **Lesson?** AWS SNS sometimes **plays hard to get**, and you have to manually confirm subscriptions if things don’t go smoothly.  


## **Major Things I Learned**
🔥 **1. Debugging Feels Like a Contestant Storming Off the Villa**  
- My SNS **first email got unsubscribed** without warning—had to **use a secondary email** to fix it.  
- Lambda **rejected my `pytz` library** because it wasn’t installed—so I **manually added it** through AWS Layers.  
- **Lesson?** AWS will throw curveballs, **but if you’re persistent, you’ll find a way back into the villa.**  

⏳ **2. Just Do It (Even If It’s Ugly)**  
- I was **procrastinating on debugging** because it felt overwhelming.  
- Realized that **learning is messy but it’s how you get better**.  
- AWS makes **you fight for every win**, but when you fix something, **the confidence boost is unreal**.  

---

## **How to Improve This (Love Island Style)**
🏆 **Next Season Upgrades**  
- **"Casa Amor" Mode**: Expand to other sports (NFL, Premier League) for more notifications.  
- **"Coupling Up" Feature**: Personalize alerts by allowing users to **choose favorite teams via DynamoDB**.  
- **"ITV Studio Setup"**: Build a simple UI where users can **subscribe/unsubscribe** easily instead of manual AWS SNS setup.  

---

## **Final Thoughts**
This project pushed me to **understand AWS Lambda in real-world debugging scenarios**, **secure cloud workflows**, and **work with APIs effectively**. If you’re a beginner, **think of cloud services like a TV production team**—each tool has a role, and when they work together, **you get an effortless, automated system**.  

AWS **Lambda, SNS, and EventBridge** are powerful tools, but they don’t work on vibes alone—you gotta understand them.  

If you’ve made it this far, you’re either:  
✅ A sports fan curious about AWS  
✅ A beginner looking for a fun way to learn AWS services  
✅ Just here for the Love Island jokes  

If you want to learn **AWS, debugging, and notifications in a way that ACTUALLY makes sense**, feel free to **check out the repo & message me!** 🚀  

