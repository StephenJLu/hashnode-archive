---
title: "How I made a basic email contact form with SendLayer API and Cloudflare Pages"
seoTitle: "Email Contact Form with SendLayer + Cloudflare Pages"
seoDescription: "Learn how to create a basic contact form using SendLayer API and Cloudflare Pages, with step-by-step instructions and code snippets"
datePublished: Sun Dec 22 2024 00:02:18 GMT+0000 (Coordinated Universal Time)
cuid: cm4yugas0001409me5yqd9p8w
slug: how-i-made-a-basic-email-contact-form-with-sendlayer-api-and-cloudflare-pages
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734822879684/775b5a0b-7108-4e76-a075-968c6d810e27.png
tags: cloudflare, contact-form, sendlayer

---

> This article outlines the process of integrating a basic contact form on a website using the SendLayer API and Cloudflare Pages. It explains how to create a SendLayer API key, configure an environment variable in Cloudflare, and connect the app to the variable. The guide provides code snippets for implementing the form and emphasizes the importance of keeping sensitive information secure.

# The Idea

I wanted to add a basic contact form to my website using SendLayer API. I already use Cloudflare Pages to serve my websites, so the integration using environment variables was a nice benefit, and made the integration a lot easier. Here’s how I did it in three simple steps.

*Note: If you’re looking to create your own form, you can use the same method with any service that provides email delivery via API. Just make sure that you meet the payload requirements when your app POSTs or otherwise.*

# The Execution

## Step One: Create a SendLayer API Key

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734823578419/53c9a3a6-56b0-4c1a-ac9c-7f6a01a0a67f.png align="center")

Head over to your SendLayer settings to create a new API key for your application.

## Step Two: Set Up and Environment Variable in Cloudflare

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734823708053/f61de765-9147-4d02-b984-c02c2bacf144.png align="center")

Head over to Cloudflare Pages, select your project, then go to Settings. Under Variables and Secrets, click on `+Add` to add a new secret. Make sure that it matches the API from SendLayer, with no extra spaces or characters.

After you set up an environment variable, you’ll need to create a deployment in order to make it effective. I recommend checking the deployment details for your variable before implementing your app.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734824898904/4a25a6cb-0b7b-44db-aa7b-f18a96d51845.png align="center")

## Step Three: Link Your App with the Environment Variable

In your code, you’ll need to insert a reference to Cloudflare’s environment variables to access the secret. In my case, Cloudflare Pages is on a Remix framework, so I needed `@remix-run/cloudflare` available.

```typescript
/* example-contact-form.tsx
...
Imports as necessary
...
*/
import { json } from '@remix-run/cloudflare';
/* Primary action function */
export async function action ({ request, context }: 
{ request: Request, context: any }) {
/* SendLayer's API Endpoint. Adjust based on documentation */
const sendLayerEndpoint = 'https://console.sendlayer.com/api/v1/email';
/*...
...
...*/
try {
    const response = await fetch(sendLayerEndpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
/* Retrieving the environment variable here */
        'Authorization': `Bearer ${context.cloudflare.env.SL_API_KEY}`,
      },
/* Make sure your payload meets the requirements. 
This particular example meets SendLayer's
required payload */
      body: JSON.stringify({
        "from": {
          "name": "Example Contact Form Submission",
          "email": "no-reply@example.com"
        },
        "to": [
          {
            "name": "John Doe",
            "email": "john@example.com"
          }
        ],
        "subject": "New Contact Form Submission",
        "ContentType": "HTML",
        "HTMLContent": `<html><body>
          <p>You have a new contact form submission:</p>
          <p><strong>Name:</strong> ${name}</p>
          <p><strong>Email:</strong> ${email}</p>
          <p><strong>Message:</strong><br/>${message}</p>
        </body></html>`,
        "PlainContent": `You have a new contact form submission:        
        Name: ${name}
        Email: ${email}
        Message:
        ${message}`,
        "Tags": [
          "testing",
          "example contact form"
        ],
        "Headers": {
          "X-Mailer": "example.com",
          "X-Test": "test headers"
        }
      }),
    });
/* Error handling and console checks */
    if (!response.ok) {
      const errorData = await response.json();
      console.error('SendLayer Error:', errorData);
      return json({ error: 'Failed to send email. 
        Please try again later.' }, { status: 500 });
    }

    console.log('Received POST request');
    console.log('Name:', name);
    console.log('Email:', email);
    console.log('Message:', message);
    return json({ success: true }, { status: 200 });

  } catch (error) {
    console.error('Error sending email:', error);
    return json({ error: 'An unexpected error occurred.' }, 
        { status: 500 });
  }
};
/* And now, your contact form itself! */
export const Contact = () => {
...
...
...
}
```

# Fin…

That’s it! Three easy steps. If you want to test your app before going live, you can create a `dev.vars` file in the root folder of your project.

*Remember <mark>.gitignore .vars</mark>! You don’t want to expose secrets.*

```plaintext
//dev.vars//

SL_API_KEY=ENTER_YOUR_SECRET
```

If you want to see it in action, head over to my website, [https://www.StephenJLu.com/contact](https://www.StephenJLu.com/contact), and send me a quick hello to tell me it worked ;)