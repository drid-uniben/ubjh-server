Alright let's do this step by step but starting from the backend
This is the next implementation after the whole review system for reviewing the manuscripts, I want to be able to publish the manuscripts as articles on my journal
Check the context.md file as context to get the whole idea of what I want to do.
I want to be able to publish articles and then using a worker do all this
DOIs: Zenodo
Indexing: Google Scholar, BASE, CORE
Preservation: PKP PN, Internet Archive
Citations for APA7, MLA, harvard and other neccesary ones
See this four sections above DOI, Indexing, Preservation and citations that's what I'm implementing now since they are all free to do.
As you'll see in my integrations.md I've made research about all this, all you have to do is search online and confirm all this and even after dropping the code implementation for all this you should assume that I already did the external setup for all this but still send me the steps to confirm I did the external setup right.
I want you to know I have signed up for zenodo just tell me how to sign up to confirm I did it right and then how to get the necessary credentials needed for the API, I want to be able to do all this using agenda and the worker whenever I publish an article to avoid long loading when I click on publish.
For the worker when I publish an article if there're any of the processes that might be beneficial as a background process when I click on publish articles then add them to the worker, also do it in a way where for failed jobs there's a controller that can check for those jobs and then retry them so in my frontend I'll have a button to check for and retry failed jobs
Now use the JavaScript server as context, I'll need to be able to able to handle the publishing of an article. There would be fields like pages number the start and end,
Admin should choose type of article to publish — research articles, review articles etc, will be sort of a drop down.
I'll need a public endpoint where people can submit their email only for subscription to email alerts, so whenever I publish an article they get an email notifying them of it and the link to the article published.
There should probably be no features to edit a published article since at first publish it's given a doi so we have to get it right.
Now you should know from the context.md you can either publish an article for an author with no manuscript, good for special publication and migrating from old system and then you can publish an article that has been approved by the admin in final decisions, the way it works is maybe there's a controller to get manuscripts for publication and then you'll see the list in the front-end probably with a pending status because the final decisions controller should create an article document for that manuscript that was approved populating it with necessary fields from the manuscript and then when you click on one of them in the frontend it opens up and populate the fields there but those fields can be edited and then the admin fills in the other fields not in the manuscript like the page number, type of article whether review, research etc. then after other fields have been manually inputted by the admin then he can choose the volume and issue to publish too and publish it there.
Infact I think that's the first thing to do when you want to publish an article you'll first choose the volume and issue number to publish too before the process of publishing it both for publications coming from manuscripts or not. There would be a way in the front-end to create volume, issues and the custom set year & month/ current year/month it was created, just like the publish date for articles being able to seta custom date for migrating old articles from old system or current date for newly published articles.
You should note that my context.md might be using different words like journals instead of articles, it might not even mention articles so ignore those, you have to do something that integrates with this my current project using my existing manuscript, user, review, articles models.
I need you to use everything available as context, think and reason carefully.
Now for the front-end.
After all these are done in the backend I want you to send the necessary endpoints that'll be added into the api.ts file for API integrations for all this. You'll see in my archives in the front-end I need to be able to upload a picture for each volume, integrate that in the relevant controller in the backend.
Now for the admin section where the upload and every other thing takes place
Make sure all screen sizes are responsive for what you are building. Alright for the frontend of this in the pages to be used as context, (I like the UI of the client repo site that uses javascript even tho I'm using typescript now, the main color of this project is that maroon color you would have seen in the reviewer-invite page, so you use variants of that if that's possible and design the pages creatively and intuitively). But I want you to design the page more creatively and intuitively (The form fields to publish articles should follow what's in the backend, also use the backend to first add the necessary endpoints that'll will be used to the api.ts file, don't forget I want it to be responsive for all screen sizes with no layout issues too. Think and reason carefully. Now for the public pages section
I need a better improved mobile friendly, responsive and creatively and intuitively designed page(s) following the existing code structure and design structure and API usage, you can say what I want is a touch up (not a complete code and design re-write) or UI and UX improvements of the page(s) to compete with high industry standards. Also note that this is a journal site, so put that in mind when considering high level industry standards. Maybe add hover effects on the articles card if available and need be and other necessary components if any?. send all the updated files for the archives page , the current-issue page and the article [id] page and this time with no dummy data but using the API integrations from the backend for these pages to display the articles that has been published and remember this are public pages so their endpoints which will be created first will be public. Just like the context document only the admin and author can see the analytics like views and downloads and number of citations gotten on their articles and the admin can see for all authors. Man's sure the citation workflow aligns with the backend Think and reason carefully and send only the relevant parts of the code not the full code. before you start all this just generate a very brief plan on everything you need to do from start to finish both for the backend and the frontend in steps, like from the first step till the last step, just a short document, to avoid hallucinations later if you hit a limit for generating and need to continue later so it can be referenced. (use deep reason).
Think and reason carefully, take your time.

This is a comprehensive request for implementing article publication with DOI integration, indexing, preservation, and citation management. Let me break down what needs to be done:
Backend Requirements:

Article Publication System:

Publish manuscripts that have been approved (status: approved) as articles
Manual article upload for special publications/migration
Choose volume and issue before publishing
Create volumes and issues with custom or current dates
Fields: pages (start, end), article type (research, review, etc.)
Generate DOI via Zenodo API
No editing after publication (since DOI is assigned)

Worker/Agenda Jobs:

DOI registration (Zenodo)
Google Scholar indexing metadata
BASE/CORE harvesting setup
Internet Archive preservation
Citation generation (APA, MLA, Harvard, etc.)
Email notifications to subscribers
Failed job retry mechanism

Email Subscription:

Public endpoint for email subscription
Send notifications when articles are published

Integrations:

Zenodo API for DOI
Google Scholar meta tags
OAI-PMH endpoint for BASE/CORE
Internet Archive upload
Citation formatting

Analytics:

Views tracking
Downloads tracking
Citations count
Only visible to admin and article authors

Frontend Requirements:

Admin Pages:

Volume/Issue management (create, edit, set cover image)
Publish article form (from approved manuscripts or manual)
Choose volume/issue
Fill article details (type, pages, etc.)
Retry failed jobs button
Responsive design with maroon color scheme

Public Pages Updates:

Archives page (with volume cover images)
Current issue page
Article detail page
All using real API data, no dummy data
Implement sleek, contemporary design with intuitive navigation
Ensure seamless cross-device experience
Integrate interactive citation management features

API Integration:

Develop comprehensive endpoint collection in api.ts
Create secure public access for article browsing
Implement robust authentication for administrative publishing actions

Implementation Plan: Article Publication System with DOI Integration
Backend Implementation Steps
Phase 1: Database Models & Core Structure

Create Volume model (with cover image field)
Create Issue model (linked to Volume)
Update Article model (add DOI, volume, issue, article type, pages, publication metadata)
Create EmailSubscriber model
Create FailedJob tracking model

Phase 2: Volume & Issue Management

Create volume controller (CRUD operations, cover image upload)
Create issue controller (CRUD operations, link to volume)
Add routes for volume/issue management

Phase 3: Article Publication Controllers

Create publication controller for approved manuscripts
Create manual article upload controller
Get manuscripts ready for publication endpoint
Publish article endpoint (creates Article from Manuscript or manual data)

Phase 4: Zenodo DOI Integration

Create Zenodo service (API integration)
DOI generation function
Register article with Zenodo
Store DOI in Article model

Phase 5: Indexing & Preservation Services

Create Google Scholar meta tags generator
Create OAI-PMH endpoint for BASE/CORE harvesting
Create Internet Archive upload service
Create citation formatter service (APA, MLA, Harvard, etc.)

Phase 6: Agenda Worker Jobs

Configure Agenda job for DOI registration
Configure job for indexing metadata generation
Configure job for preservation uploads
Configure job for email notifications to subscribers
Add failed job tracking and retry mechanism

Phase 7: Email Subscription System

Create email subscriber controller (subscribe/unsubscribe)
Create notification email service for new publications
Public subscription endpoint

Phase 8: Analytics & Citation

Update view tracking for articles
Add download tracking
Add citation count tracking
Citation download endpoints (BibTeX, RIS, etc.)

Phase 9: Routes & Integration

Add all publication routes
Add public article viewing routes
Add analytics routes (protected)
Add subscription routes

Frontend Implementation Steps
Phase 1: API Integration File

Add all backend endpoints to src/services/api.ts
Define TypeScript interfaces for Volume, Issue, Article, etc.

Phase 2: Admin - Volume & Issue Management

Create Volume management page (/admin/volumes)
Create Issue management page (/admin/volumes/[volumeId]/issues)
Add cover image upload for volumes
Responsive design with maroon theme

Phase 3: Admin - Article Publication

Create publication dashboard (/admin/publications)
List approved manuscripts ready for publication
Create publish article form (select volume/issue, add metadata)
Manual article upload form
Failed jobs retry interface

Phase 4: Public Pages - Archives

Update archives page with volume cover images
Fetch volumes and issues from API
Display articles grouped by volume/issue
Mobile-friendly, responsive design

Phase 5: Public Pages - Current Issue

Update current issue page with real API data
Display latest published articles
Add hover effects and modern UI touches
Responsive layout

Phase 6: Public Pages - Article Detail

Update article detail page with real data
Add citation download buttons (APA, MLA, etc.)
Display DOI and metadata
Show analytics (views/downloads) for authorized users only
Mobile-optimized reading experience

Phase 7: Email Subscription

Create subscription form component
Add to public pages (footer or sidebar)
Success/error notifications

External Setup Verification Steps
Zenodo Setup

Confirm sandbox account creation
Generate API access token
Verify deposit:write and deposit:actions scopes
Test with sandbox environment first

Google Scholar

Verify HTML meta tags implementation
Ensure Highwire Press tags are correct
Wait for Google crawler indexing

BASE & CORE

Confirm OAI-PMH endpoint is accessible
Register journal with BASE
Register journal with CORE
Provide OAI-PMH URL

Internet Archive

Create Internet Archive account
Generate S3-like API keys
Verify programmatic upload permissions

This plan ensures a systematic approach from database models to worker jobs to frontend UI, with all integrations properly configured. Let me know when you're ready to proceed with the implementation!
