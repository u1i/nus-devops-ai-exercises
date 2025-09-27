# Hands-On Exercise: Exploring and Using CDNs

**Course:** DevOps and Cloud with AI   
**Module:** Foundations of Distributed Systems - DNS Fundamentals

## Introduction

This exercise provides a practical look at Content Delivery Networks (CDNs). We will first explore how a major website (CNN.com) uses CDNs to deliver content efficiently. Then, we will deploy our own simple static website to a global CDN using the free service Surge.sh. This will help illustrate the concepts of performance optimization and scalability discussed in the course.

## Prerequisites

*   A modern web browser (like Chrome, Firefox, Edge).
*   Node.js and npm installed on your computer. (Needed for Part 2). If you don't have them, download from [https://nodejs.org/](https://nodejs.org/) (LTS version recommended) or use the [Google Cloud Shell](https://ssh.cloud.google.com/).
*   A terminal or command prompt.

## Part 1: Exploring CDNs on the Web (CNN.com)

In this part, we'll investigate how a high-traffic website like CNN uses CDNs.

1.  **Visit CNN:** Open your web browser and go to [https://www.cnn.com](https://www.cnn.com).
2.  **Open an Article:** Click on any news article link on the homepage.
3.  **Inspect an Image:** Find an image within the article. Right-click on the image and select "Open image in new tab" or "Copy image address" (options may vary slightly by browser).
4.  **Observe the URL:** Look at the URL of the image in the new tab or the copied address. You'll likely see that it doesn't start with `www.cnn.com` or `cnn.com`. Instead, it probably starts with something like `media.cnn.com`.

    *Example URL might look like:* `https://media.cnn.com/api/v1/images/stellar/prod/xxxxxxxx.jpg?q=w_xxxx,h_xxxx,x_xxxx,y_xxxx,c_fill`

5.  **Why a Different Domain?** The `media.cnn.com` subdomain is a strong indicator that CNN is serving its static media assets (images, videos) from a different system than its main website content. This system is almost certainly a Content Delivery Network (CDN).

**Understanding CDN Concepts:**

*   **Offloading:** By serving images from `media.cnn.com`, CNN offloads the heavy lifting of delivering these large files from its main application servers. The main servers can focus on generating the article text, handling user interactions, and serving dynamic content.
*   **Global Distribution & Proximity:** CDNs have servers located in many datacenters across the world (Points of Presence - PoPs). When you request an image from `media.cnn.com`, the CDN directs your request to the server geographically closest to you. This reduces latency (delay) and speeds up loading times significantly.
*   **Caching:** Once an image is requested from a specific region, the CDN server in that region caches (stores) a copy. Subsequent requests for the same image from users in that same region can be served directly from the cache, which is much faster than fetching it from the original source server again.

**Connection to Modern Web Apps:**

Traditionally, every click might have resulted in a full page request to the main server. Modern frontend frameworks (like React, Vue, Angular) often build Single Page Applications (SPAs). The initial load might fetch the core application code (HTML, CSS, JavaScript). These static application files themselves are often hosted on a CDN for fast initial loading. Subsequent interactions then typically involve making API requests to the backend server just to fetch or update *data*, not entire pages. This architecture leverages CDNs heavily for performance.

*(Optional: For a deeper dive, you can open your browser's Developer Tools (usually F12), go to the 'Network' tab, and reload the CNN article page. You'll see requests going to various domains, including CDNs, for different assets like images, scripts, and stylesheets.)*

## Part 2: Deploying Your Own Static Site to a CDN (Surge.sh)

Now, let's deploy a very simple static HTML file to a global CDN using Surge.sh. Surge is a simple, free service for deploying static web projects.

1.  **Install Surge:** If you haven't already, open your terminal or command prompt and install Surge globally using npm:
    ```bash
    npm install -g surge
    ```

2.  **Create Project Folder:** Create a new folder somewhere on your computer for this exercise. Let's call it `my-cdn-test`.
    ```bash
    mkdir my-cdn-test
    cd my-cdn-test
    ```

3.  **Create `index.html`:** Inside the `my-cdn-test` folder, create a file named `index.html`. Open it in a text editor and add the following minimal HTML content:
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>My CDN Test</title>
    </head>
    <body>
        <h1>Hello from the CDN!</h1>
    </body>
    </html>
    ```
    Save the file.

4.  **Deploy with Surge:** Make sure you are still in the `my-cdn-test` folder in your terminal. Run the deploy command:
    ```bash
    surge
    ```

5.  **Surge Prompts:**
    *   **Email/Password:** If this is your first time using Surge, it will prompt you to create a free account by entering an email and password.
    *   **Project Path:** It will show the current directory (`/path/to/your/my-cdn-test`). Press Enter to confirm.
    *   **Domain:** Surge will suggest a random subdomain (like `random-words.surge.sh`). You can accept it by pressing Enter, or type your own unique subdomain name and press Enter.

6.  **Analyze the Output:** Surge will upload your file and then show output similar to this (your domain and details will differ):

    ```
             domain: your-unique-name.surge.sh  <-- Your live site URL
               size: 1 files, 116 bytes          <-- Size of your project
             upload: [=========================] 100%
                CDN: [=========================] 100%  <-- CDN propagation status

      ┌──────────────────────────────────────────────────────────────────────────────┬────────────────────┐
      │  Certificate: issuer=C = GB, ST = Greater Manchester, L = Salford, O = Sec…  │  Valid             │
      │   *.surge.sh, surge.sh                                                       │  XX more days      │
      └──────────────────────────────────────────────────────────────────────────────┴────────────────────┘
      ┌──────────┬──────────────────────────────────────────────────────────────────────────────┬─────────┐
      │          │   ns1.surge.world   ns2.surge.world     or CNAME…                            │         │
      │    NS    │   ns3.surge.world   ns4.surge.world     geo.surge.world                      │         │
      ├──────────┼──────────────────┬───────────────────────┬─────────────────────┬─────────────┼─────────┤
      │   HTTP   │   sfo.surge.sh   │   US, San Francisco   │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   lhr.surge.sh   │   GB, London          │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   yyz.surge.sh   │   CA, Toronto         │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   jfk.surge.sh   │   US, New York        │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   ams.surge.sh   │   NL, Amsterdam       │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   fra.surge.sh   │   DE, Frankfurt       │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   sgp.surge.sh   │   SG, Singapore       │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   blr.surge.sh   │   IN, Bangalore       │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   syd.surge.sh   │   AU, Sydney          │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      │   HTTP   │   nrt.surge.sh   │   JP, Tokyo           │   XXX.XXX.XXX.XXX   │   Provider  │   ✔ ◍   │
      └──────────┴──────────────────┴───────────────────────┴─────────────────────┴─────────────┴─────────┘
       Live preview ............................................. XXXXXXXXXXXXX-your-unique-name.surge.sh
       Production ............................................................. your-unique-name.surge.sh

       Success! - Published to your-unique-name.surge.sh
    ```

    *   **Key Observations:**
        *   Notice the `CDN: [===] 100%` line. Surge explicitly tells you it's pushing your content to its CDN edge locations.
        *   Look at the list of servers under `HTTP`. It shows server locations across the globe (San Francisco, London, Singapore, Tokyo, etc.). This demonstrates the global distribution aspect of a CDN. Your `index.html` file is being copied to these locations.

7.  **Verify Deployment:** Open your web browser and navigate to the `your-unique-name.surge.sh` domain provided in the output. You should see your "Hello from the CDN!" message. Wow! Your simple webpage is now live on a global CDN.

**Optional Next Step:**
Surge allows you to use custom domains. If you owned `mydomain.com`, you could configure a CNAME DNS record for `static.mydomain.com` to point to `na-west1.surge.sh` (or the appropriate Surge endpoint) and then deploy using `surge ./ my-static-site.mydomain.com`. This allows you to run parts of your website entirely from a CDN under your own domain!

## Summary

In this exercise, you observed how real-world websites leverage CDNs for performance and saw firsthand how easy it can be to deploy your own static content to a global CDN. This reinforces the importance of CDNs for optimizing the delivery of web assets, reducing latency, and improving the scalability of web applications, as discussed in Module 18.