Twelfth phase Email. 

Eleventh phase Email. Request edit access. Here authenticated user can request to edit a note. A `request-edit` route is created. A `request-edit` controller is created that sends an email requesting to edit the note to the owner of the email, quoting the email address of the user who is requesting. Nuxt UI change in the View Note page. A "Request to Edit" button is added to the view right next to "Unshare" and the "Enable Edit" button. Once button is clicked user is led to the "Are you sure you want to request edit access?". If user answers "Yes" they are led to a page which confirms that the request has been sent. If user answers "No" they are simply returned to the previous page. Owner receives email with the user requesting edit access and the link to the note.<!--The email message template in the share controller is updated with a link that a user can click to request edit access for the note.--><!--In the email, a link with the `enable-edit` route appended with the user email is provided in the message-->

Tenth phase Email. `share` controller updated with `sendEmail` function that sends email with the link to the note to all the authenticated users' email. Nuxt UI no change needed.

Ninth phase Email. Setup Configure Brevo. Add A Brevo Email Service. Brevo service will have a `sendEmail` function that sends an email to a user. Test Brevo. Create Test Email Route. Create Test Email Controller. Test Email Service. Delete test files.

Eighth phase Share. `editor` field added to `Notes` collection. It's of type email. `enableEdit` controller updated so that it sets a given note's `canEdit` field to `true` and writes an email string in the `editor` field of the Notes collection. `update` method is overridden. Note owner can still edit note or user whose email matches the string in the `editor` field && `canEdit` field is `true`. Nuxt UI is updated. The "Enable Edit" button will include an email form input. Above the email form input put a label like "Enter email of user who you want to give Edit rights"

Seventh phase Share. `enable-edit` route is created. `enableEdit` controller created. `enableEdit` controller where only the owner of a note sets a shared note's `canEdit` field to `true`. `share` controller updated so that it sets a given note's `isShared` field to `true` and `canEdit` field to `false`. Nuxt UI change. The "Enable Edit" button should appear next to the "Unshare" button. Owner clicks "Enable Edit" and is taken to the "Are you sure you want this note to be editable?" page. Clicking "Yes" leads user to Confirmation page. Clicking "No" returns user to the Note view page.

Sixth phase Share. `update` method is overriden in the Notes controller. Such that either the note owner can edit the note or if the `canEdit` field is set to `true` anyone can edit it. No Nuxt UI changes (Yeah yeah I know other users will stll be able to see the "Unshare" and "Delete" links but not be able to do anything with them. A sin I know, however any code changes done to address these things will be premature at this point.) Sharing is still done manualy.

Fifth phase Share. `canEdit` boolean field is added to Notes controller in Strapi Admin. Default value of `canEdit` is `false`. <!--`enable-edit` route created. `enableEdit` controller created which sets a given note's `canEdit` field to `true`. Once a note is editable--> `share` controller updated so that it sets a given note's `isShared` and `canEdit` field to `true`. `unshare` controller updated so that it sets a given note's `isShared` and `canEdit` fields to `false`. No Nuxt UI changes. Manual share.

<!--
Fifth phase Share. `update` method is overriden in the Notes controller. Now only the note owner can edit the note. Shared notes can only be viewed others not edited, deleted or unshared by others. No Nuxt UI changes (Yeah yeah I know other users will stll be able to see the "Unshare", "Delete" and "Edit" links but not be able to do anything with them. A sin I know, however any code changes done to address these things will be premature at this point.) Sharing is still done manualy.
-->

Fourth phase Share. Only the owner can unshare a note. Debatable, as a case for other users being able to unshare a note can be made. However, I personally believe that only the owner should unshare the note. The `unshare` controller needs to be updated to allow only the note owner to unshare a note. No Nuxt UI changes (Other users will probably just see a blank page if they try to unshare a note, will deal with that later). Sharing is still done manually.

Third phase Share. `unshare` route created. `unshare` controller created which sets a given note's `isShared` field to `false`. Update Nuxt UI. In the note view page, if note is shared there should be an "Unshare" button, else it's a "Share" button. Add a "Are you sure you want to unshare?" page. Unshare confirmation page with link to return to the note or return to all notes. Sharing is still done manually

Second phase Share. `isShared` field added to Notes controller. Default value is `isShared: false`. `share` route created. `share` controller created which sets a given notes `isShared` field value to `true`. Once a note is shared it can never be unshared. Override the `findOne` controller so that only note owners can view the notes they own unless the note's `isShared` field is `true`. Update Nuxt UI with "Share" button. Are you sure you want to Share page. Share confirmation page with link to the note displayed. Sharing is still done manually.

First phase Share. Delete method is overriden in the Notes controller. Now only the note owner can delete the note. No valid rationale to be honest. I just personally believe that only the note owner should be able to delete a note. No UI changes.

Zeroth phase Share. User only just needs to share the link to the note they own to another user. Immediately, the authenticated user has view access, edit access and delete access to the note. No need for share or unshare routes. No need for Share or Unshare buttons. Sharing is done manually (outside the app) by copying the link to the article and sending it to the intended recipient. No UI changes.

^First phase Share. The Share button should appear in the frontend Nuxt UI on the view page of the note. User should click the share button. That should lead them to a page where the user is asked whether or not they really want to share the note. If the answer is "Yes" the note is shared and the user is led to a page confirming that the note has been shared. The page will simply show the user the link to the note. If the answer is "No" the user is led back to the View note page. When the user revisits the link to the note, there will be an "Unshare" button

Error Handling. Only the owner can share and unshare a note.

Sequence of Events. Email functionality should only be added once sharing functionality is complete. One less thing to think about.

Sequence of Events. this is a partial look. Make sure you do CSS (Flat Minimal Design) before adding JavaScript then add a Text Editor plugin (Tip Tap or Quill) then add Infinite Scrolling. A minimal but effective version of error handling is done prior to CSS.

Sharing should also be split as follows. First phase, Owner shares a note. What happens next is that any authenticated user can view the note. Or Owner shares a note then any authenticated user can view (`findOne`) and edit (`update`) the note. Before implementation make sure.

Remove error handling for auth phase of note-taking app. Limit it to just a user being able to only view notes they have own. The other error checks can be handled later. Also remove the middleware. That can be done later. Right now we are focused on the logic that completes the app (Note Taking -> Auth -> Sharing). Not so much on making sure every case is dealt with. Always work in a forgiving manner

As a revision the app development could have been split in the following manner. It starts out as a Note taking app then becomes an Authenticated Note taking app. Followed by it becoming a Note Sharing App where shared notes can only be viewed. That is, an owner can share or unshare a note. Shared notes can only be viewed not edited. After that, the Note Sharing App will have editing as well where owners can add or remove editors for a note. The next step would then be user

App starts out as a Note taking app then becomes an Authenticated Note taking app and then becomes an Authenticated Note Sharing app.

With regards to this Note Sharing App project. The approach I am taking is first writing it out as I go then capturing any insights as well. 

`temp.md` is a file meant to work as a pastebin. So any information I get is pasted into it to make the editing process easier. Things like prompts, answers to prompts, text copied from the web and so on.

