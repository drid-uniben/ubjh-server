
First prompt:

Now I want to create a context aware prompt from the next prompt I'll send next, meaning that you'll check the relevant files necessary to gain context, ask me clarifying questions first too and then describe what needs to be done and how it could be done as a prompt and then add this prompt to the end of the .md file you just created. this is the prompt: Then I want to be able to implement a workflow where the manuscript can be archived but then the archive is then going to be divided into two sections, the first section is going to be for the manuscript with 20-30%, archiving a manuscript under that section involves uploading the plagiarism report and then it sends the author a mail saying he can revise the manuscript and then he gets his login credentials and is able to login into his dashboard and then submit the revised manuscript, meaning in his dashboard, he'll see the manuscript in the manuscript section of the dashboard with the archive status and it'll have a submit revision button attached to it and then clicking on that and then uploading the revised manuscript will then create it as a new submission using all the previous info of the first manuscript, then going to the manuscript page will then show two manuscripts there, and then the first manuscript will then show the archived status without any submit revision button this time, then there's the second option in the archiving and that's for above 30% ,for those one they author get a message saying their manuscript have been archived then there would be the reason field and then the reason will be typed there and then clicking on archive sends the email which will also contain the credentials to login into the dashboard and also telling them to submit again during the next call for papers and then there would be a button to submit a manuscript which will be inactive till the the next call for paper.

My follow up prompt that contains what I'm focusing on for now:


Alright, I want to forget all the parts of the archiving manuscripts and also the parts of the plagiarism and all, this is the main part I want to focus on, the idea is that right now, forgetting probably logic I might have done to account for something, in this my current system I want it to work with only one reviewer decision submission which can either be a reviewer or the admin, I want you to check the admin dashboard in the frontend, there you'll see the All reviews section, now I want to make it robust, when you get there I want you to see some action button or a section containing a button that says send review to author, they'll be an option with send with option to receive revision or send without option to receive revision from the author, clicking on this sends a mail to the author telling them to login into their dashboard check the review and either send a revision or not, if the author credentials haven't been sent to them yet then this should first send them their credentials mail before the review mail. Then I want to then do the author's dashboard, both the frontend and the backend where they'll see the review and the place to submit them, I need the way for the review to be submitted to be in a way where the old manuscripts are still accessible somewhere, help me ask me clarifying questions, I'm still forming the full plan, "Manage Co-authors" Section: A new section in the author dashboard will be created to: * List the author's manuscripts. * Display all associated co-authors for a selected manuscript. * Provide an interface to edit and complete the information for incomplete co-authors, which will trigger a PUT request to the new /co-authors/:id endpoint. just handle it in the frontend for the author dashboard that if the status is reconciliation then it should still show under review in the frontend. Now since I don't still want to mess up existing logic for the normal two or three people review should it needed later, I fill I'll have to create a new status for the manuscript that it'll change to when the admin uses the new section in the All reviews section to send revision or rather send reviews button, also that new status should be accepted in places where the status of a completely finished reviewed manuscript that has gone through all the peer review is needed to access the section, I don't know if it's the final decision or the publication section or I don't know. I still want that context aware prompt but let's first build a complete plan, check all the relevant files first to understand me and ask questions, I still want something not too complicated, simple enough.


The model reply next:

I have analyzed the current implementation of both the frontend (admin and author components) and the backend (models,  controllers, and services).

  To build a complete and robust plan for the "Send Review" workflow, I need to clarify a few architectural and
  behavioral details. Here is my analysis and the questions I have:

  Current Assessment
   1. Status Flow: You want to add a new state between "Review Complete" and "Final Decision" where the admin can
      communicate the review to the author.
   2. Admin Logic: The admin needs a "Send Review" action. This logic must:
       * Check for existing author credentials and send them if they are missing.
       * Trigger an email notification about the review.
       * Set the manuscript to a new status that marks it as "Review Shared" or "Awaiting Revision."
   3. Author Dashboard:
       * The "Manage Co-authors" section will allow authors to fix incomplete co-author records (which currently creates         a User from an IncompleteCoAuthor record).
       * Authors need a way to submit revisions that preserves history (creating a new manuscript record linked to the
         old one).
   4. Frontend Consistency: You want internal statuses like reconciliation to be abstracted as Under Review for the
      author to maintain simplicity.

  ---

  Clarifying Questions

  1. The New Status
  What should we call the new status? I recommend REVIEW_COMMUNICATED (for when no revision is requested) and
  AWAITING_REVISION (when the author is asked to resubmit).
   * Does this naming work for you, or do you have a specific term in mind?

  2. Credential Sequence
  When an admin clicks "Send Review," if the author doesn't have credentials yet:
   * Should it send two separate emails (Credentials first, then Review Notification)?
   * Or should we create a combined template that provides both login details and the review information in one go?

  3. "Only One Reviewer" Requirement
  You mentioned the system should work with "only one reviewer decision submission which can either be a reviewer or the  admin."
   * In the "All Reviews" section, if there are multiple reviews, should the admin select one specific review to send,
     or does it send a summary of all completed reviews?
   * Should the "Send Review" button only appear once at least one review is completed?

  4. Revision History
  When an author submits a revision and a new manuscript record is created:
   * What should happen to the original manuscript's status? Should it be marked as ARCHIVED or SUPERSEDED?
   * How should the link between them be displayed to the author? (e.g., "Revision 1", "Original Submission").

  5. Co-author Management Permissions
   * Should the "Manage Co-authors" interface be available for all manuscripts an author has, or only those currently in     the AWAITING_REVISION or SUBMITTED state?

  6. Author Dashboard UI
  For the reconciliation -> Under Review mapping:
   * Are there any other internal statuses you want to simplify for the author? (e.g., Should REVIEW_COMMUNICATED just
     show as "Decision Pending" until a final decision is made?)

  7. Next Status Transition
  After the review is sent (and possibly a revision is submitted), what is the next step in the workflow?
   * Does the new status (REVIEW_COMMUNICATED) allow the admin to move directly to the "Final Decisions" / "Publication"     section, even if no revision was requested?

  Once you provide feedback on these, I can draft the comprehensive "Context Aware Prompt" that describes the full
  implementation plan across the frontend and backend.


My next prompt:

Alright I don't want a new state in between I just want the current sections where they check if you have been already been reviewed before you can access it, I just want you to tell me those sections so that when I reach there I can then add a logic to accept this new state too to have the same access than the reviewed has. Yes and I want the reviewer and admin to be able to see the revision submitted by the author so they can give their decision/review again again if needed, I need to know if the reviewer dashboard handles this, both the backend and the frontend, even the admin assignment section. For the questions, 1. Yes, maybe it's the review communicated status that should be the similar to the new state I talked about above that'll have the same access as the reviewed status, also after awaiting revision status when the author submits the revision and the reviewer submits a new review what is suggested to happen next? the All reviews section in the admin, the send review button there should change it to review communicated if no revision is needed for the new revision submitted? 2. Credentials first, then Review Notification 3. At least one reviewer, if there are multiple review then it should have a custom field to input a summary for the notes to the author that the reviewers sends and it's than comment that the author sees on their dashboard, also for multiple reviews it should show options of the reviewers who submitted the review for the admin to choose who the revision that would be submitted by the author should go to, they can also select both reviewers if the admin wants to. Yes send review should appear when at least one review is completed. 4. It shouldn't be marked as archived, maybe a new status, I still want it accessible, Yes I like your example of the link between the authors. 5. All manuscripts that the author has. 6. Yes decision pending 7. Yes it does. 

The incomplete reply but you can do something with it, follow its recommendations: 

I've identified the key sections that restrict access based on the "reviewed" status and analyzed how revisions are
  currently handled.

  Where to Update Status Logic
  The following sections in the backend currently check for "reviewed" status (or the collection of statuses like
  approved, rejected, minor_revision, major_revision) and will need to be updated to accept the new REVIEW_COMMUNICATED
  status:

   1. Final Decisions Controller (src/Review_System/controllers/finalDecisions.controller.ts):
       * getManuscriptsForDecision: The aggregation pipeline here filters for manuscripts ready for a final decision. We         should add REVIEW_COMMUNICATED to the match criteria so manuscripts with this status still appear in the
         decision queue.
   2. Manuscript Reviews Controller (src/Review_System/controllers/manuscriptReviews.controller.ts):
       * getAllManuscriptReviews: The status === 'reviewed' filter mapping (lines 154-159) needs to include
         REVIEW_COMMUNICATED.
       * getStatistics: The logic that increments the reviewed count (lines 316-322) needs to include the new status.
   3. Override Decision Controller (src/controllers/overrideDecision.controller.ts):
       * This controller allows admins to force a status. It should be updated to allow moving to or from
         REVIEW_COMMUNICATED.
   4. Frontend Admin Dashboard:
       * src/app/admin/reviews/page.tsx: The status badge logic and filters need to account for REVIEW_COMMUNICATED
         (which you want to show as "Decision Pending" to the author, but likely "Communicated" to the admin).

  ---

  Reviewer Dashboard & Revision Handling
  Currently, the system is not fully optimized for your requested revision-as-a-new-record flow:

   * Reviewer Dashboard (src/Review_System/controllers/adminReview.controller.ts & review.controller.ts):
       * They currently look for a revisedPdfFile on the same manuscript.
       * They do not automatically look for previous manuscripts via a revisedFrom link in the dashboard view. We will
         need to update the getReviewWithHistory method to follow the revisedFrom chain so reviewers can see the
         original submission side-by-side with the new revision record.
   * Admin Assignment (src/Review_System/controllers/assignReview.controller.ts):
       * The assignReviewer logic doesn't currently check if a manuscript is a revision of an older one. We need to add
         logic here so that when a revision is submitted, the admin is prompted (or it's defaulted) to assign it to the
         same reviewer(s) who handled the original.

  ---

  Questions & Proposed Workflow

  1. After Revision & New Review
  You asked: "After awaiting revision status when the author submits the revision and the reviewer submits a new review,  what is suggested to happen next?"
   * Recommendation: Once the new review is submitted for the revision, the status of the new manuscript record should
     automatically move back to UNDER_REVIEW (while being reviewed) and then to REVIEW_COMMUNICATED (once the admin
     clicks "Send Review" again) or directly to APPROVED/REJECTED if the admin is ready for a final decision.
   * Admin UI: In the "All Reviews" section, the button should always allow "Send Review" as long as there is a new
     completed review that hasn't been shared yet.

  2. Handling Original Manuscripts
  For the "old" manuscript that was revised:
   * How about a status called SUPERSEDED? It's clearer than HISTORY. It signifies that a newer version exists, but the
     record remains "Reviewed" and accessible.

  3. The Review Selection Logic
  When an admin selects which reviewer(s) should receive the new revision:
   * Should we store this selection in the Manuscript model (e.g.,


