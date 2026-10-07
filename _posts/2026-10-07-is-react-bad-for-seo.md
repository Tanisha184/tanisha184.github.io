---
layout: post
title: "Is React Bad for SEO?"
date: 2026-10-07 12:00:00 +0600
categories: [engineering]
excerpt: "React is not inherently bad for SEO. What matters is how public pages are rendered—CSR, SSG, or SSR—and whether crawlers get complete HTML."
image: /assets/images/blog/is-react-bad-for-seo/whatsapp-link-previews.png
---

If someone told you “We shouldn’t use React because it’s bad for Google,” you’re not alone. That concern comes up in client calls, RFPs, and conversations between marketing and development teams.

Here’s the reassuring part:

**React is not inherently bad for SEO.**

The concern usually comes from a particular way React applications can be built: Client-Side Rendering (CSR), where the browser receives a basic HTML document and JavaScript, then React generates much of the page content in the browser.

For a public marketing website, that can create unnecessary challenges. Search engines and link-preview systems may receive an initial HTML response that contains little of the actual page content, while the visitor's browser has to run JavaScript before the complete page appears.

But React does not have to work that way.

## What people are actually worried about

When someone says “SEO,” they usually mean a few practical things:

- Will Google receive the right page title, description, and content?
- When we share a link on WhatsApp, Facebook, or LinkedIn, will the preview show the correct title, description, and image?
- Will visitors see meaningful content quickly instead of waiting for JavaScript to construct the page?

Those are reasonable concerns. They affect search visibility, sharing, performance, and ultimately how trustworthy the website feels.

![WhatsApp link previews showing different titles and descriptions for Export Sheba About and Blog pages](/assets/images/blog/is-react-bad-for-seo/whatsapp-link-previews.png)

*Different URLs can show different preview titles and descriptions when Open Graph metadata is present in the HTML response.*

The important question is not:

**“Are we using React?”**

It's: “How is our React application delivering the public page?”

## The simple idea: when is the page “finished”?

Think of your website like a brochure.

### Approach A: Build the brochure in the visitor's browser

The server sends a basic HTML document along with JavaScript instructions.

The visitor's browser downloads and runs that JavaScript. React then creates the page content: the headline, paragraphs, images, navigation, and other elements.

This approach is called Client-Side Rendering (CSR).

It's perfectly normal for many web applications, especially dashboards and tools where the user is logged in and the content is highly interactive.

But for a public marketing site, it means the initial HTML response may not contain all of the content that matters.

### Send a finished brochure first

Instead of asking every visitor's browser to construct the initial page, the website generates the HTML ahead of time.

Each public URL can have a complete HTML document containing its:

- page title
- description
- main content
- canonical URL
- social sharing metadata

That HTML is deployed as part of the website and can be served directly from a CDN.

React can still take over afterward to make the page interactive. Menus, forms, buttons, animations, and other application behavior can still work normally.

This approach is called Static Site Generation (SSG).

For a public marketing site, this removes one major class of rendering-related SEO concerns because the important content is already present in the initial HTML.

## The three terms your developer might use

**CSR: Client-Side Rendering**

The browser receives the application and React generates much of the page in the browser.

Good fit: interactive applications, dashboards, internal tools.

**SSG: Static Site Generation**

The site generates HTML during the build process, before deployment.

Those generated HTML files can then be served directly from static hosting or a CDN.

Good fit: marketing sites, documentation, blogs, landing pages, and other mostly public content.

**SSR: Server-Side Rendering**

A server generates the HTML when a visitor requests a page.

This can be useful when the HTML needs to depend on information that is only known at request time, such as personalized content, authentication, frequently changing data, or other request-specific information.

The important distinction is:

- CSR: the browser generates the page
- SSG: the HTML is generated ahead of time
- SSR: the HTML is generated when the request arrives

## So, is React bad for SEO?

**No.**

React itself does not determine whether your public website is SEO-friendly.

The rendering and delivery strategy matters.

If your public pages arrive as complete HTML, search engines and other systems can access the page content and metadata without depending entirely on the browser to construct the initial page.

That means you can use React for the interactive experience while still delivering a strong HTML foundation for your public pages.

## “But we are using React. Is our site OK?”

If your public website uses static generation or server-side rendering, React itself is not preventing search engines from receiving your page content.

For example, on Oneiroi Systems, our public pages are prerendered during the build process. The generated HTML is deployed as static files and served to visitors. React then hydrates that existing HTML in the browser to provide the interactive experience.

So the initial page isn't dependent on React creating the entire page from an empty HTML shell.

Google and other systems can access the page's intended metadata and content directly from the HTML response.

## You can sanity-check this yourself

Open a live public page and use **View Page Source**.

You should be able to find things such as:

- `<title>...</title>`
- `<meta name="description" ...>`
- `<link rel="canonical" ...>`
- `<h1>...</h1>`

The important point is that the actual page content should be present in the raw HTML, not only appear after JavaScript runs.

![Terminal output showing title, Open Graph title, and canonical link tags in the HTML for Export Sheba about page](/assets/images/blog/is-react-bad-for-seo/html-metadata-curl-check.png)

*The same metadata appears in the first HTML response—you can also verify it from the command line with `curl`.*

For example, our deployed Export Sheba `/en/about` page returns a page-specific title, canonical URL, Open Graph metadata, and the actual page heading/content in the HTML response.

## When is a different approach better?

Not every part of a business needs to be rendered the same way.

A public marketing website and an internal dashboard have very different requirements.

### Public marketing website

Usually cares about:

- search visibility
- sharing
- fast initial content
- stable public URLs
- page-specific metadata

SSG can be an excellent fit because the content is mostly public and can be prepared before visitors arrive.

### Admin dashboard or internal tool

Usually cares more about:

- authentication
- user-specific data
- highly interactive interfaces
- live application state

CSR can be completely appropriate here.

The point isn't that one rendering method is universally better.

The right rendering strategy depends on what the page needs to do.

## What to ask your development team

If you want a quick sanity check, these questions are enough:

1. Are our public pages delivered with the main content already present in the initial HTML?
2. Does each important page have its own title, description, canonical, and social sharing metadata?
3. Do we have a sitemap listing the public URLs we want search engines to discover?
4. When content changes, what process rebuilds and redeploys the public HTML?

![Terminal output listing loc entries from Export Sheba sitemap.xml](/assets/images/blog/is-react-bad-for-seo/sitemap-xml-loc.png)

*A sitemap lists the public URLs—and locale alternates—you want crawlers to discover.*

You don't need to choose React vs. WordPress vs. another technology simply because one technology has a reputation for being “better for SEO.”

The more useful question is:

**“When someone requests our public page, do they receive a complete page or an application that has to construct the page in the browser?”**

## The bottom line

**React doesn't decide your SEO. Your rendering architecture does.**

A React application can use Client-Side Rendering, Static Site Generation, Server-Side Rendering, or a combination of approaches.

For a public marketing website with mostly static content, pre-rendered HTML can provide a strong foundation for SEO, social sharing, and fast initial content while still letting React handle the interactive parts of the site.

So when someone says:

**“It's built with React, so SEO will be a problem.”**

The better follow-up question is:

**“How are the public pages rendered?”**

If the answer is “They are prerendered and the important content is already in the HTML,” then React itself is not the thing standing between the website and good SEO.
