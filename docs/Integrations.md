Integrations.md

Here are the steps to integrate the Zenodo API into your journal backend to upload articles and receive a DOI.
The process doesn't involve a formal "application." You get API access by generating a personal access token from your Zenodo account. The DOI is then automatically reserved and registered as part of your API upload workflow.
Step 1: Get Your API Access Token
This is the "key" your backend will use to authenticate with Zenodo.

- Create an Account: If you don't already have one, sign up for a Zenodo account.
- Go to Applications: Log in and go to your Settings > Applications.
- Create a Token:
  - Find the "Personal access tokens" section.
  - Click "New token."
  - Give it a descriptive name (e.g., "My Journal Backend").
  - Crucially, select the required scopes: You will need deposit:write and deposit:actions.
  - Click "Create."
- Save Your Token: Zenodo will show you the token only once. Copy it immediately and store it securely (e.g., as an environment variable) in your backend's configuration. This is your ACCESS_TOKEN.
  Step 2: Use the Sandbox for Testing
  Do not test on the live Zenodo site. Use the dedicated Sandbox environment, which works exactly the same way but won't create permanent, public DOIs.
- Sandbox Site: https://sandbox.zenodo.org/
- Sandbox API Base URL: https://sandbox.zenodo.org/api/
  You must create a separate account on the Sandbox and generate a separate API token there (following Step 1 on the sandbox site).
  Step 3: The API Workflow to Get a DOI
  Here is the high-level process your backend code will follow for each journal article. All API requests must include your token in the header:
  Authorization: Bearer YOUR_SANDBOX_ACCESS_TOKEN

1. Create a New "Deposition" (A Draft Upload)
   First, you tell Zenodo you want to start a new upload.

- Action: Send a POST request to https://sandbox.zenodo.org/api/deposit/depositions
- Body: Send an empty JSON body: {}
- Response: Zenodo will respond with a JSON object for the new deposition. The two most important pieces of information are:
  - id: The unique ID for this deposition (e.g., 12345).
  - links.bucket: The URL where you will upload your files (e.g., https://sandbox.zenodo.org/api/files/some-unique-uuid).

2. Upload Your Files
   Now, upload the journal article (e.g., article.pdf) to the bucket URL you received.

- Action: Send a PUT request to the links.bucket URL, adding your desired filename at the end.
  - URL: [links.bucket]/article.pdf
  - Body: The raw binary data of your PDF file.
    You can repeat this step for any other files (e.g., supplementary data).

3. Add Metadata
   This is where you add the article's title, authors, abstract, etc.

- Action: Send a PUT request to the deposition's URL: https://sandbox.zenodo.org/api/deposit/depositions/12345 (using the id from step 1).
- Body: A JSON object containing all your metadata. This is the most critical step.
  Example Metadata JSON:
  {
  "metadata": {
  "title": "A Study on the Effects of Code on Coffee Consumption",
  "upload_type": "publication",
  "publication_type": "article",
  "description": "This paper explores the critical relationship between software development and caffeine intake.",
  "creators": [
  {
  "name": "Doe, Jane",
  "affiliation": "University of Technology",
  "orcid": "0000-0002-1825-0097"
  },
  {
  "name": "Smith, John",
  "affiliation": "Software Institute"
  }
  ],
  "keywords": ["backend", "journal", "api", "coffee"],
  "access_right": "open"
  }
  }

- Reserving a DOI: The response from creating the deposition in Step 1 will already contain a doi or prereserve_doi field. This is the DOI that will be registered upon publication. You can use this "reserved" DOI immediately in your article's PDF if needed.

4. Publish the Deposition
   This is the final step that makes the record public and officially registers the DOI. This action is irreversible.

- Action: Send a POST request to the links.publish URL from the deposition object: https://sandbox.zenodo.org/api/deposit/depositions/12345/actions/publish
- Response: You will get the final, published deposition object. The doi field will now show the registered DOI (e.g., 10.5072/zenodo.12345), and the state will be "done."
  Once you have perfected this workflow in the Sandbox, you can switch to the production API by:
- Using the production API token you generated in Step 1.
- Changing the base URL from https://sandbox.zenodo.org/api/ to https://zenodo.org/api/.

You are correct about the citations, but several of the other services do require an external setup, just a different kind of setup than a simple API key.
Here’s a confirmation for each item on your list.
DOIs

- Zenodo: Requires External Setup.
  - As we just discussed, this is the one that definitely requires external credentials. You must create a Zenodo account and generate a Personal Access Token to use as your API key.
    Indexing
- Google Scholar: No External Credentials.
  - This is not an API integration. Google Scholar finds and indexes content by crawling the web. Your "setup" is purely internal: your backend must generate the correct HTML <meta> tags (like Highwire Press tags or DC.identifier) on each article's public-facing webpage. If Google's crawler can find your pages and read those tags, you'll get indexed.
- BASE (Bielefeld Academic Search Engine): Requires External Setup (Registration).
  - BASE doesn't use an API key. It harvests content using the OAI-PMH (Open Archives Initiative Protocol for Metadata Harvesting) standard.
  - Internal Work: You must build an OAI-PMH endpoint in your backend (e.g., https://your-journal.com/oai).
  - External Work: You must then go to the BASE website and register your journal as a content provider by submitting your OAI-PMH endpoint URL.
- CORE: Requires External Setup (Registration).
  - This works just like BASE. You must register your journal with CORE as a content provider and give them your OAI-PMH endpoint so their harvesters can collect your metadata.
    Preservation
- PKP PN (Public Knowledge Project Preservation Network): Requires External Setup (Integration).
  - This is a preservation network (based on LOCKSS) primarily for OJS (Open Journal Systems).
  - Since you are building a custom backend, you cannot just "send" them your articles. You would need to contact the PKP team to see if your custom system can be integrated into their network. This is a formal integration process, not a simple API.
- Internet Archive: Requires External Setup (for programmatic uploads).
  - If you just want your journal to be passively crawled, that requires no setup (but it's not guaranteed).
  - If you want your backend to actively and programmatically upload every new article for preservation (which is what you'd want), then yes, you need credentials. You would need to create an Internet Archive account and get API keys (specifically, S3-like access and secret keys) to upload files.
    Citations
- APA7, MLA, Harvard, etc.: No External Setup.
  - You are 100% correct here. These are formatting styles, not services. This is a purely internal code task. Your backend will take the article's metadata (author, title, year, DOI) and use a library (like citation-js for Node.js) to format that data into different citation strings.
  -
