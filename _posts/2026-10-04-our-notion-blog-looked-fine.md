---
layout: post
title: "Our Notion Blog Looked Fine Until Every Image Started Vanishing"
date: 2026-10-04 12:00:00 +0600
categories: [engineering]
excerpt: "Production images broke without a code deploy—Notion signed URLs expired. Here is the build-time R2/CDN pipeline and content-hash fix."
image: /assets/images/og-notion-post.png
---
<img width="1470" height="835" alt="Screenshot 2026-09-15 at 9 52 13 PM" src="https://github.com/user-attachments/assets/373c614a-edea-4425-ad43-d0cec1c48bea" />

Last month our live blog looked broken, and I hadn’t changed a single line of code.

Cover photo: gone. Hero: empty box with alt text. Everything else on the page worked. Only the images had vanished.

It’s not the end of the world, but on a company site it looks sloppy. That’s a bad first impression.

### The Root Cause: Expiring Links

The CMS was fine. Notion was fine. The links were not.

We use Notion as the CMS—the content database behind our site. Writing and publishing felt easy. Then images started failing in production.

What I didn’t grasp at first: when you attach an image in Notion, the URL isn’t permanent. It’s a **temporary signed link**, so it expires after a limited period of time. After that, the built site can still store the old URL in JSON or HTML. 

The browser requests it:
* Broken image.
* Page loads.
* Hero is empty.
* Alt text sitting there.

Not a great look.

### Two Separate Problems

There was a second layer: speed and control. We were still depending on Notion-hosted URLs for those assets. Even with Cloudflare in front of the site, you can’t fix this with "better caching" when the URL itself is designed to expire.

So we had two problems, not one:
1. Images disappear after the signed URL expires.
2. We didn’t control the production copy of the asset—we were basically renting a link from Notion.

Naming the problem clearly was the easy part. Shipping a fix that stayed fixed took longer than I expected.

### The Build-Time Pipeline

It wasn’t one image in one field. Card thumbnails live in Notion properties. Inline images sit inside article HTML converted from Notion blocks. A single post can carry several URLs, all on the same expiry clock.

We needed **build-time automation**, not a one-off download.

**Our Stack:** React + Vite static site · Notion fetched at build time · Sharp (WebP) · Cloudflare R2 · GitHub Actions on every deploy.

On sync/build, the pipeline downloads each image from Notion while the temporary URL is still valid, optimizes it, and uploads it to R2 with long-lived cache headers. 

The built site no longer stores Notion links. It stores our paths, served in production through the CDN.

> **Flow:** Notion → GitHub Actions → R2 → CDN → website

The live site no longer depends on Notion for serving images—only for authoring content.

### Handling Updates with Hashing

Then the next issue showed up: *what if someone replaces the image in Notion?*

If we kept reusing the same filename or URL, the browser or CDN could keep showing the old image, especially with aggressive caching for performance.

So we added **hashing from the actual image bytes**:
* New image → new hash → new path.
* *Example:* `/blog/my-post-slug/card-a83f21.webp`

The new asset gets a new URL, so immutable caching stays safe without manually busting anything. We also keep a small manifest in CI so we’re not re-downloading everything when nothing changed. When the bytes change, the hash changes, and the site picks it up.

### Final Thoughts

Even though this started as a "small" problem, it forced me to separate two things:
* Notion is great for managing content.
* It’s not where I want production images to live.

Of course, I later realized there are CMS platforms that already handle image hosting, transforms, and permanent URLs. *Of course I did.*

I’m still glad we built it. Sometimes solving a small problem yourself teaches you much more about how the whole system works. That’s worth the hours. 

If you’ve hit expiring asset URLs with Notion or another headless CMS, I’d love to hear how you solved it in the comments!
