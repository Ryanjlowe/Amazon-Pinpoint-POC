# Amazon Pinpoint POC Guide

Amazon Pinpoint is a multi-channel digital engagement service. It is a part of the Customer Engagement suite of services, enabling customers to send both transactional and promotional messages across email, SMS, push notification, voice, and custom channels.

This repository assumes a base familiarity with the service and if you have not already done so it is recommended that you use the getting-started material below.

## Introduction to Amazon Pinpoint

If you are not familiar with Amazon Pinpoint you can learn more about this tool on these pages:

* [Product Page](https://aws.amazon.com/pinpoint/)
* [GitHub Reference Architectures](https://github.com/aws-samples/digital-user-engagement-reference-architectures)
* [Product Docs](https://docs.aws.amazon.com/pinpoint/)

### Amazon Pinpoint Definitions

* **Project** - a collection of settings, customer information, segments, and campaigns
* **Endpoint** - represents a destination that you can send messages to, such as a mobile device, email address, or phone number
* **User** - an individual who has a unique user ID, associated with zero or more endpoints
* **Segment** - a group of your customers that share certain attributes
* **Dynamic Segment** - a segment that uses selection criteria that you specify and is based on endpoint data that's reported by your project
* **Imported Segment** - a segment that uses selection criteria that you specify and is based on endpoint definitions that you import from a file. Imported segments are static; they don't change over time
* **Journey** - an automated workflow that performs a series of messaging activities for an Amazon Pinpoint project
* **Campaign** - a messaging initiative that engages a specific segment of users for an Amazon Pinpoint project
* **Template** - a set of content and settings that you can define, save, and reuse in email messages, push notifications, SMS text messages, and voice messages for any of your Amazon Pinpoint projects
* **Channel** - a type of platform that you can deliver messages to. For example, use the email channel to send email to newsletter subscribers, use the SMS channel to send SMS text messages to your customers, or use a push notification channel to send push notifications to users of your iOS or Android app.

## POC Goals

By the end of this POC, you should have performed the following actions:

1. Deploy the Digital User Engagement Events Database
1. Enable the Email and SMS Channel
1. Add Templates
1. Upload segments
1. Create Dynamic Segments
1. Create a Campaign
1. Create a Journey


## Deploy the Digital User Engagement Events Database Solution

Analytics and Reporting should be considered from the very beginning of any Pinpoint Project.  Pinpoint has the ability to stream engagement events out  of Pinpoint in realtime via the Pinpoint Event Stream.  This needs to be configured right away when creating a project as engagement events will be discarded until the Event Stream is configured.

For this POC, we will use the Digital User Engagement Events Database to setup and create the Pinpoint Project as well as enable the Pinpoint Event Stream and create a database that is queryable.

1. Navigate to the Solution Page: https://aws.amazon.com/solutions/implementations/digital-user-engagement-events-database/
1. Choose **Launch in the AWS console** to be taken into AWS CloudFormation.
1. On the **Create Stack** page,  choose **Next**.
1. Enter a **Stack name** and then choose **Next**.
1. Choose **Next** on the **Configure stack options** screen.
1. Last check the box next to **I acknowledge that AWS CloudFormation might create IAM resources.** and choose **Next**.

After a few minutes, the stack will be completely deployed and ready to go.  The full Solution implementation guide can be found here:  https://docs.aws.amazon.com/solutions/latest/digital-user-engagement-events-database/welcome.html


## Enable the Email and SMS Channel

1. Navigate to the **My Pinpoint Project** in the [Amazon Pinpoint Console](https://console.aws.amazon.com/pinpoint/), then **Settings**.
1. Enable the channels - TODO

## Create Templates

For each of the templates below, create them -  TODO

#### Upsell Template

[Template File](templates/1_upsell.html)

**Subject:** Did you know about our Premium Subscription Plan {{User.UserAttributes.FirstName}}?

#### Renewal 10% Off Template

[Template File](templates/2_renew_10.html)

**Subject:** {{User.UserAttributes.FirstName}} Renew Now and get 10% Off!

#### Renewal 30% Off Template

[Template File](templates/3_renew_30.html)

**Subject:** {{User.UserAttributes.FirstName}} Renew Now and get 30% Off!


#### Renewal Reminder Template

[Template File](templates/4_renewal_reminder.html)

**Subject:** {{User.UserAttributes.FirstName}} Did you forget to renew?


## Import Endpoint Data

You can create two types of segments in Amazon Pinpoint. You can import a segment from a CSV or JSON via the console or programmatically via the APIs. **Imported segments** are static, i.e. they never change.

We will now use the sample endpoint file in this repository to create endpoints with a variety of different endpoint attributes.

1.  Download the [asset_endpoints.json](asset_endpoints.json) from this repository
1. Navigate to the **My Pinpoint Project** in the [Amazon Pinpoint Console](https://console.aws.amazon.com/pinpoint/), then **Segments**.
1. Choose **Create a segment**.
1. Select **Import a segment**.
1. Choose **Choose files** and select the **asset_endpoints.json** file downloaded above.
1. Choose **Create segment** to begin importing.

## Create Dynamic Segments

The other type of segments are Dynamic segments.  Dynamic segments are based on attributes that you define and can change over time. When you add new endpoints to Amazon Pinpoint, or if you modify or delete existing endpoints, the number of endpoints in your dynamic segment may increase or decrease.

We will now create three new dynamic segments by applying attribute filters to the existing endpoints preloaded into the project.  These segments will later be used in Journeys and Campaigns.

#### Renewal Eligible Segment
1. Navigate to the **My Pinpoint Project** in the [Amazon Pinpoint Console](https://console.aws.amazon.com/pinpoint/), then **Segments**.
1. Choose **Create a segment**.
1. Select **Build a segment**.
1. Provide the name **Renewal Eligible** into the name field.
1. Configure **Segment Group 1** to add segment filters.
   1. Under the **Add filters to refine your segment** choose **Filter by user**.
   1. For the **Choose an user attribute** dropdown choose **Stage**
   1. Ensure **Is** is chosen in the middle dropdown.
   1. In the **Choose values** dropdown choose **Renewal**.
1. Click **Create Segment** to create your first dynamic segment.  Note, a pop-up will appear highlighting that this segment targets multiple endpoint channels, not just Email.  Select **I Understand**.  Alternatively, we can add a **Filter by Channel** and select the Channel **Email** explicitly in the **Segment Group**.

#### Upsell Eligible Segment
1. While still in the Project **My Pinpoint Project**.
1. Choose **Create a segment** from the **Segments** screen.
1. Select **Build a segment**.
1. Provide the name **UPL Upsell Eligible** into the name field.
1. Configure **Segment Group 1** to add segment filters.
   1. Under the **Add filters to refine your segment** choose **Filter by user**.
   1. For the **Choose an user attribute** dropdown choose **Stage**
   1. Ensure **Is** is chosen in the middle dropdown.
   1. In the **Choose values** dropdown choose **Upsell**.
   1. Choose **+ Add an attribute or metric**
   1. For the **Choose an user attribute** dropdown choose **LoanType**
   1. Ensure **Is** is chosen in the middle dropdown.
   1. In the **Choose values** dropdown choose **UPL**.
1. Click **Create Segment** to create your second dynamic segment.  Note, a pop-up will appear highlighting that this segment targets multiple endpoint channels, not just Email.  Select **I Understand**.  Alternatively, we can add a **Filter by Channel** and select the Channel **Email** explicitly in the **Segment Group**.

#### High Value Customer Segment
1. While still in the Project **My Pinpoint Project**.
1. Choose **Create a segment** from the **Segments** screen.
1. Select **Build a segment**.
1. Provide the name **High Value Customers** into the name field.
1. Configure **Segment Group 1** to add segment filters.
   1. Under the **Add filters to refine your segment** choose **Filter by user**.
   1. For the **Choose an user attribute** dropdown choose **CLVTier**
   1. Ensure **Is** is chosen in the middle dropdown.
   1. In the **Choose values** dropdown choose **High**.

1. Click **Create Segment** to create your third dynamic segment.  Note, a pop-up will appear highlighting that this segment targets multiple endpoint channels, not just Email.  Select **I Understand**.  Alternatively, we can add a **Filter by Channel** and select the Channel **Email** explicitly in the **Segment Group**.


## Create a Campaign
Engage your audience by creating a messaging campaign. A campaign sends tailored messages on a schedule that you define. You can create campaigns that send push notifications, email, SMS text messages, and voice messages.

To experiment with alternative campaign strategies, set up your campaign as an A/B test, and analyze the results with Amazon Pinpoint analytics.

We will now go through the steps to set up a campaign.

#### Create a Campaign

1. Navigate to the **My Pinpoint Project** in the [Amazon Pinpoint Console](https://console.aws.amazon.com/pinpoint/), then **Campaigns**.
1. Choose **Create a campaign**.
1. Configure the campaign name, type, and channel:
   1. You can choose the name freely, i.e. "**UPL Upsell Eligible**"
   1. Select "**Standard Campaign**" as type
   1. Select "**Email**" as channel
   1. Choose **Next**.
1. Select **Use an existing segment** and select the dynamic segment **UPL Upsell Eligible** you created earlier. Choose **Next**.
1. In the **Create your message** step choose **Choose a template** which will open up a new modal window.
   1. Choose the messaging template **UPL_Upsell** and choose **Choose template**.
   1. Back on the **Create your message** step choose **Next**.
1. Now you **Choose when to send the campaign**. Dynamic segments can be triggered by events. Choose **At a specific time**.
  * Note: When using a dynamic segment, like we are, the criteria for that segment will be processed at time of campaign send.  In this way, you can configure a campaign to have a recurrence in which the segment criteria will be evaluated before each send.  This is useful for Welcome campaigns, for instance, where everyday all new segment endpoints will be sent a message.
1. Look at the different options on this screen.  You can set the campaign to execute immediately by choosing **Next**.
1. On the **Review and launch** screen, look over all of the configured options prior to launching the campaign.  Proceed to select **Launch the Campaign**.
1. On the **Campaign Details** screen you can review all the settings, Duplicate the campaign to send again, Delete the campaign to clean up old entries, and view the Campaign Metrics.  Select the **Campaign Metrics** link.
1.  On the **Campaign Dashboard** screen you can review all of the analytics related to the campaign that was just sent.  We are using simulated endpoint addresses, so all messages will be successfully delivered with no opens or clicks.

## Create a Journey

A journey is a customized, multi-step engagement experience. To create a journey, start by choosing a segment that defines which customers will participate in the journey. Then add the activities that customers pass through on their journeys. Activities can include sending messages or splitting customers into groups based on their behaviors.

We are going to create a Renewal Journey to send a renewal email to all of our customers who are identified by the **Renewal Eligible** dynamic segment.  For our high value customers, identified by the **High Value Customers** segment, we are going to offer them 30% off, where as  our low value customers will receive 10% off.  In addition, if our high value customers do not open the email after 3 days, we will send them a reminder email.

1. Navigate to the **My Pinpoint Project** in the [Amazon Pinpoint Console](https://console.aws.amazon.com/pinpoint/), then **Journeys**.
1. Follow the guided help to see all of the Journey features.
1. Give the Journey the name **Renewal Journey** by replacing the **Untitled** text.
1. Define a Journey entry criteria
   1. Choose **Set entry condition**
   1. Under **Segments** choose **Renewal Eligible** dynamic segment created earlier.
   1. Under **Specify how often to add new segment members** choose **Once every** then choose **1** and **days**.
    * This will evaluate the criteria for the dynamic segment everyday and push new endpoints into this journey.
   1. Choose **Save**
1. Add a decision split to branch the journey based on High Value vs Low Value customers.
   1. Choose **Add activity** directly under the **Journey entry** activity
   1. Under **Choose a journey activity** choose **Yes/no split**.
   1. Under **Select a condition type** choose **Segment**.
   1. Under **Segments** choose the **High Value Customers** dynamic segment created earlier.
   1. Choose **Save**
1. Add an email activity for our high value customers
   1. Choose **Add activity** directly under the **Yes** split path.
   1. Under **Choose a journey activity** choose **Send email**.
   1. Choose **Choose an email template** and select **Renew_30_percent_off** and choose **Choose template**.
   1. Choose **Save**.
1. Add an email activity for our NOT high value customers
   1. Choose **Add activity** directly under the **No** split path.
   1. Under **Choose a journey activity** choose **Send email**.
   1. Choose **Choose an email template** and select **Renew_10_percent_off** and choose **Choose template**.
   1. Choose **Save**.
1. Configure a reminder email for our high value customers if they do not open the first email after 3 days.
   1. Choose **Add activity** directly under the **Send email** activity on the **Yes** path.
   1. Under **Choose a journey activity** choose **Yes/no split**.
   1. Under **Select a condition type** choose **Event**.
   1. Under **Events** choose **Email open**.
   1. Under **Activity** choose **Renew_30_percent_off**.
   1. Under **Condition evaluation** choose **Evaluate after**.
   1. Choose **3** and **days**
   1. Choose **Save**
1. Add an email activity for the high value customers who did not open the first email.
   1. Choose **Add activity** directly under the new **No** split path.
   1. Under **Choose a journey activity** choose **Send email**.
   1. Choose **Choose an email template** and select **Renew_reminder_percent_off** and choose **Choose template**.
   1. Choose **Save**.
1. Lastly, review your Journey before launching by choosing **Review**.
   1. Choose **Next** in the window that opened on the left.
   1. Review the following screen and choose the **Publish**.
   1. While the Journey countdown timer prepares to launch the journey, choose **View Metrics** and choose **Get Started**
   1. Wait for the timer to complete and then you can view the journey metrics.  Viewing each activity in the journey will show you how many endpoints each activity received.
