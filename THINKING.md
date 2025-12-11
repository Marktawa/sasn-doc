Sequence of Events. this is a partial look. Make sure you do CSS (Flat Minimal Design) before adding JavaScript then add a Text Editor plugin (Tip Tap or Quill) then add Infinite Scrolling. A minimal but effective version of error handling is done prior to CSS.

Sharing should also be split as follows. First phase, Owner shares a note. What happens next is that any authenticated user can view the note. Or Owner shares a note then any authenticated user can view (`findOne`) and edit (`update`) the note. Before implementation make sure.

Remove error handling for auth phase of note-taking app. Limit it to just a user being able to only view notes they have own. The other error checks can be handled later. Also remove the middleware. That can be done later. Right now we are focused on the logic that completes the app (Note Taking -> Auth -> Sharing). Not so much on making sure every case is dealt with. Always work in a forgiving manner

As a revision the app development could have been split in the following manner. It starts out as a Note taking app then becomes an Authenticated Note taking app. Followed by it becoming a Note Sharing App where shared notes can only be viewed. That is, an owner can share or unshare a note. Shared notes can only be viewed not edited. After that, the Note Sharing App will have editing as well where owners can add or remove editors for a note. The next step would then be user

App starts out as a Note taking app then becomes an Authenticated Note taking app and then becomes an Authenticated Note Sharing app.

With regards to this Note Sharing App project. The approach I am taking is first writing it out as I go then capturing any insights as well. 

`temp.md` is a file meant to work as a pastebin. So any information I get is pasted into it to make the editing process easier. Things like prompts, answers to prompts, text copied from the web and so on.

