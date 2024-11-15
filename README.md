# lightweight-crm
Lightweight CRM

In August 2024, I was working with Manchester Metropolitan University students on my software startup [vinr](https://www.vinr.ai/). The project focused on finding cost-effective and efficient ways of marketing a bootstrapped startup in an internet crowded by tech giants. One of the proposals was to trigger a drip campaign after a user signed up for my product. Essentially, this is an automated way to reach your users with welcome emails, offers, recommendations, etc. The emails are scheduled at a frequency that is not too annoying but nurturing.

I could have used one of the existing CRM solutions to do this. However, I have had really bad experiences integrating with CRMs such as Marketo and Salesforce when I worked for startups before. I was pretty sure that most CRM tools would feel like using a lathe to fix a small screw, especially considering my simple use case of just drip campaigns. Since I was building a B2B software that businesses use for their operations, I thought, why not include a CRM in it? This way, I could sign up for my own product and "eat my own dog food."

So, game on. I started programming an API for creating and deleting contacts remotely. This allowed anyone using the product to call these APIs and manage their contacts (name and email). API keys could be obtained from the UI.

![API](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jobq8qj5g6e3sbnf5pwf.png)

Next, I created a collection called `campaigns`, which could be populated from the UI of my product.

![Campaign](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/912fpn9bnfft2apx60dn.png)

```json
{
  "_id": "01JCQHXJDZWEN4E6JKC0752YH9",
  "name": "vinr AI Welcome mail English",
  "contactFilter": "language=en",
  "active": "on",
  "preferredSendTime": "10:00",
  "createdTime": "2024-11-08T00:27:53.034Z",
  "updatedTime": "2024-11-15T09:15:02.700Z",
  "messages": [
    {
      "subject": "Welcome to vinr AI 🚀",
      "sendDay": 0,
      "htmlTemplate": "<html lang=\"en\"></html>"
    }
  ],
  "contacts": [
    {
      "name": "John doe",
      "emailAddress": "john.doe@example.com",
      "hash": "5257447467882cc3514722abb9af36450f465ed47a365b4b578d574172cab08c",
      "createdTime": "2024-11-08T10:00:03.538Z"
    }
  ],
  "activities": []
}
```

You might notice that the `contacts` field is populated with contacts that are eligible based on the `contactFilter` criteria. This is done by a CRON which I will explain later.

One important step was handling the unsubscribe link. You might have noticed the hash in each email; this hash represents the contact 1:1. I appended an unsubscribe link with this hash. When a user clicks the unsubscribe link at the bottom of the email, a confirmation screen appears, and the contact is removed from both the campaign and the `contacts` collection, saving us from the fury of the customer.

The final step was a CRON job that runs at scheduled intervals, checking for active campaigns with the current preferred send time. The CRON collects eligible contacts based on the filter criteria and inserts them into the campaign. Finally, the campaign triggers the email API.

Cool! So, I had everything needed to send an email except the actual method to send it. All that was pending was contacting the AWS folks to get my CRM approved for SES to send bulk emails. After following the process outlined in the SES documentation to move to production, I was able to join the final piece of the puzzle.

An API for contacts, a campaign with a mail template and contact filter criteria, a CRON job, and SES integration—tada!

This lightweight CRM has automated my previously painful manual efforts.

![Mail](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/oj85ztzqgr47ibp0vxhx.png)
