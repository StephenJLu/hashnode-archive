---
title: "How I made a comments field with Cloudflare R2 Object Storage"
seoTitle: "Creating a Comments Field with Cloudflare R2"
seoDescription: "Learn how to develop a comments field using Cloudflare R2 Object Storage and Workers for efficient data management and enhanced website functionality"
datePublished: Sun Jan 05 2025 04:19:42 GMT+0000 (Coordinated Universal Time)
cuid: cm5j3t8tx000609jv3zg1ac6u
slug: how-i-made-a-comments-field-with-cloudflare-r2-object-storage
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736045996157/a76d8bac-e751-42c6-abe0-b5c295568633.png
tags: cloudflare, databases, comments, r2

---

> In this article, I share my experience working on back-end development (not my usual fare) for a non-profit organization. I focused on automating and managing their publication archive using Cloudflare's R2 Object Storage, a system for storing unstructured data. I developed an admin interface to streamline the process of uploading and organizing PDF files and enhanced the website's front-end to improve user navigation through the archive. Additionally, I explored R2 further by creating a personal project to manage comments, utilizing Cloudflare Workers for the back-end. This involved methods for storing, displaying, and deleting comments, as well as potential security enhancements using authentication and CAPTCHA. This project showcases the versatility of R2 and its ability to manage various data types efficiently.

# The Idea

In the past few weeks, I’ve been doing a lot of back-end web development for a large non-profit organization. It’s not in my usual wheelhouse, which is front-end development, but I had a good time working on these projects, which would make website maintenance and updates automatic. The web development team will now have an easier time maintaining constantly updated information relevant to the organization. Even though I upgraded a multitude of services on the back-end of their website, nothing significantly changed—at least visually—for the users of their website. I suppose that’s the point, though.

The most significant project that I finished was the management of their publication archive. This organization has publications dating all the way back to 1971, so there was an archive of more than 200 PDFs and associated thumbnails that we needed to manage. The original archive was displayed in its entirety in a table organized by year, and updating the archive had to be done manually. In addition, all the files were kept on a server and linked to the archive page through a static hyperlink. A little clunky in today’s world.

In my work, I upgraded the archive management by creating an admin interface to upload PDFs and thumbnails. On upload, the system would automatically:

* Add the PDF and thumbnail references to the database, structured by year
    
* Upload the assets into Cloudflare’s R2 Object Storage, organized by year
    
* Add the quarter and year of publication to the title based on the file name
    
    * For example, a PDF named `Nov2021.pdf` would automatically be associated with “4th Quarter 2021” in the database.
        
* Display the new publication on the front-end
    
* Update the online publication reader to display the latest release
    

In addition to displaying the entire archive, the front-end now allows a user to browse the archive by year upon selection.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736047693944/293b773d-ca77-4b73-93ec-a98ed6032edc.png align="center")

### How did I achieve this?

If you’ve been following my development journey, you know I’m a fan of Cloudflare. I was interested in their [R2 Object Storage](https://developers.cloudflare.com/r2/) service, and this perfectly fit the bill.

## What is R2?

Aside from being the first part of everyone’s favorite droid, Cloudflare’s R2 Object Storage allows developers to store and manage large amounts of unstructured data without the typical costly egress bandwidth fees. In this context, unstructured data means information that does not adhere to a pre-defined data model or format, like SQL. SQL, or Structured Query Language, uses a relational database: data is organized into tables with clearly defined rows, columns, and relationships between them. The data is specific to each column, and a database schema acts as the structure definition of all the data contained in the database. So, this makes it easy to query and analyze data, but it also means that all the data contained in the table must conform to the specified structure.

**SQL Example**

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    hire_date DATE,
    department_id INT
);
```

Unstructured data storage, like R2, doesn’t rely on schemas. You can store JSON, XML, plain text, media files (PNGs, JPGs, MP4s, WAVs, MP3s), documents, emails, backups, log files, or whatever your heart desires.

You can see how R2 *was* the solution I was looking for.

# Using R2 for Comments

I had a good time playing around with R2, so I wanted to create a personal project that utilized this work: Comments.

If you want to skip ahead and view the live test, check out [https://stephenjlu.com/r2-test](https://stephenjlu.com/r2-test). I’ve integrated it with [Cloudflare’s Turnstile](https://docs.stephenjlu.com/docs-stephenjlu/projects/how-to-implement-cloudflares-turnstile), too.

## The R2 Worker - The Back-end

The workhorse behind R2 management goes back to [Cloudflare Workers](https://developers.cloudflare.com/workers/). I enjoy this service because it’s very lightweight, fast, and serverless. It’s also easy to bind environment variables and secrets.

For the R2 Worker, there are three major parts to the function:

### CORS Headers

```javascript
const corsHeaders = {
    'Access-Control-Allow-Origin': '*', // Change to specified domain for increased security
    'Access-Control-Allow-Methods': 'GET, PUT, DELETE, OPTIONS',
    'Access-Control-Allow-Headers': 'Content-Type, X-Custom-Auth-Key',
    'Content-Type': 'application/json'
  };
  
  const createResponse = (data, status = 200) => new Response(
    JSON.stringify(data), 
    { status, headers: corsHeaders }
  );
```

### Authentication

```javascript
 const hasValidHeader = (request, env) => 
    request.headers.get("X-Custom-Auth-Key") === env.AUTH_KEY_SECRET; // Don't forget the
// super-secret key so not just anyone can mess with your data

export default {
    async fetch(request, env) {       
      if (request.method === 'OPTIONS') {
        return new Response(null, { headers: corsHeaders });
      }
      
      if (!hasValidHeader(request, env)) {
        return createResponse({ error: 'Forbidden' }, 403);
      }
```

### Operations

```javascript
try {
        const bucket = env.R2_BUCKET;
        // Reading the existing comments
        switch (request.method) {
          case "GET": {
            const file = await bucket.get('comments.json');
            const comments = file ? JSON.parse(await file.text()) : [];
            return createResponse(comments);
          }
          // Posting new comments
          case "PUT": {
            const file = await bucket.get('comments.json');
            const comments = file ? JSON.parse(await file.text()) : [];
            const newComment = await request.json();
            
            comments.push(newComment);
            await bucket.put('comments.json', JSON.stringify(comments));
            
            return createResponse({ success: true });
          }
          // Deleting selected comments
          case "DELETE": {
            const { timestamp } = await request.json();
            const file = await bucket.get('comments.json');
            const comments = file ? JSON.parse(await file.text()) : [];
            
            const updatedComments = comments.filter(comment => comment.timestamp !== timestamp);
            await bucket.put('comments.json', JSON.stringify(updatedComments));
            
            return createResponse({ success: true });
          }
  
          default:
            return createResponse({ error: 'Method not allowed' }, 405);
        } // Graceful error handling
      } catch (error) {
        console.error('Worker error:', error);
        return createResponse({ error: error.message }, 500);
      }
    }
  };
```

### comments.json

I decided to store my comments in a JSON file. I only have one, but in real-world applications, you can have as many of these as you have assets/users. This file is only 250 B.

```json
[
    { 
      "name":"John Doe",
      "comment":"Hello World!",
      "timestamp":"2025-01-05T00:10:52.860Z"
      },

    {
      "name":"Jane Doe",
      "comment":"Hey, John!",
      "timestamp":"2025-01-05T00:11:11.431Z"
    }
]
```

## Comments Form and Rendering - The Front-end

On the front-end, it’s a single form.

### Loading Existing Comments

```typescript
export const loader = async () => {
  try {
    console.log('Fetching comments...');
    const response = await fetch(STORAGE_URL); 
    console.log('Response status:', response.status);
    
    if (!response.ok) {
      console.error('Failed to fetch:', response.status);
      return json<LoaderData>({ comments: [] });
    }

    const text = await response.text();
    console.log('Raw response:', text);

    const data = JSON.parse(text);
    console.log('Parsed data:', data);

    if (!Array.isArray(data)) {
      console.error('Data is not an array');
      return json<LoaderData>({ comments: [] });
    }

    const validComments = data.filter(Boolean);
    console.log('Valid comments:', validComments);

    return json<LoaderData>({ comments: validComments });
  } catch (error) {
    console.error('Loader error:', error);
    return json<LoaderData>({ comments: [] });
  }
};
```

### Deleting Existing Comments

```typescript
export const action = async ({ request, context }: { request: Request; 
context: CloudflareContext }) => {
  const formData = await request.formData();
  const intent = formData.get('intent');

  // Handle delete
  if (intent === 'delete') {
    const timestamp = formData.get('timestamp') as string;
    if (!timestamp) {
      return json<ActionData>({ success: false, errors: 
{ comment: 'Missing timestamp for deletion' } });
    }

    const response = await fetch(WORKER_URL, {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json',
        'X-Custom-Auth-Key': context.cloudflare.env.AUTH_KEY_SECRET,
      },
      body: JSON.stringify({ timestamp })
    });

    if (!response.ok) throw new Error('Failed to delete comment');
    return json<ActionData>({ success: true });
  }
```

### Posting New Comments

```typescript
try {
    const name = formData.get('name') as string;
    const comment = formData.get('comment') as string;

    if (!name || !comment) {
      return json<ActionData>({
        success: false,
        errors: {
          name: !name ? 'Name is required' : undefined,
          comment: !comment ? 'Comment is required' : undefined,
        },
      });
    }

    const response = await fetch(WORKER_URL, {
      method: 'PUT',
       headers: {
    'Content-Type': 'application/json',
    'X-Custom-Auth-Key': context.cloudflare.env.AUTH_KEY_SECRET,
  },
      body: JSON.stringify({ name, comment, timestamp: new Date().toISOString() }),
    });

    if (!response.ok) throw new Error('Failed to save comment');
    return json<ActionData>({ success: true });
  } catch {
    return json<ActionData>({ success: false, errors: { comment: 'Failed to save comment' } });
  }
};
```

### Rendering Comments

```typescript
export default function Comments() {
  const loaderData = useLoaderData<typeof loader>();
  const actionData = useActionData<ActionData>();
  const navigate = useNavigate();  

// Refreshing after posting or deleting
  useEffect(() => {
    if (actionData?.success) navigate('.', { replace: true });
  }, [actionData?.success, navigate]);

  return (
    <div style={{ 
      display: 'flex', 
      flexDirection: 'column',
      alignItems: 'center',
      padding: '6rem'
    }}>

{/* Form rendering */} ...

{/* and rendering existing comments */} ...

                <strong>{comment.name}</strong>
                <p style={{ margin: '0.5rem 0' }}>{comment.comment}</p>
                <small style={{ color: '#666' }}>
                  {new Date(comment.timestamp).toLocaleString()}
                </small>
              </div>

{/* Other rendering */}
```

### Comments Protection

It’s not used in this example for simplicity, but you can protect comments behind a login or CAPTCHA, like Cloudflare’s Turnstile. I added Cloudflare Turnstile to my [comments demo here](https://stephenjlu.com/r2-test).

# The Power of R2

R2’s power is in its flexibility. You can store a variety of objects in a database and manipulate the data to your heart’s desire, if it fits your use case. For a publication archive and a fun, little personal project, R2 worked wonders.

If you’re interested in diving deeper, check out the full documentation of my project and the GitHub Repo.

[Full Documentation](https://docs.stephenjlu.com/docs-stephenjlu/projects/using-cloudflare-r2-object-storage-to-serve-a-comments-field)

[GitHub Repo](https://github.com/StephenJLu/comments-r2)