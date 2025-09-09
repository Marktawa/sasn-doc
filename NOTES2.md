## Article: Part 1

- Introduction: Description of article
- Introduction: Feature overview/list
- Introduction: Demo video
- Introduction: Part 1 overview/feature list
- Introduction: link to video, repo, live link
- Prerequisites (Node, GitHub, Facebook, Google?, Strapi Cloud, Brevo)
- Create Strapi app
- Create admin in terminal
- Admin Dashboard login
- Create Notes collection with title(short text), content(long text) fields, user one to many relation field
- Add Authenticated User permissions for Notes (find, findOne,  create, delete, update)
- Register an OAuth App in GitHub
- Copy `Client ID` and `Client Secret`
- Configure Provider in Strapi
- Add sample users using GitHub auth
- Add sample entries to Notes collection for each user
- Test find and findOne for each user. Indicate problem of user accessing other user's notes. Suggest a fix
- Create controller to override find, findOne methods. Add logic to validate whether someone owns queried notes.
- Retest find and findOne for each separate user
- Create controller override for create method
- Create controller override for delete method
- Create controller override for update method 



## Strategy

- Install Strapi
- Create admin
- Create Notes collections
- Add permissions public and auth
- Add auth providers
- Add entries (users, notes)
- Add find, findOne, create, delete, update handlers in controller
- Test API using curl (crud)

- Install Nuxt
- Create home page
- Create login page
- Auth integration and redirects
- Create notes list page
- Create individual note page
- Create Create note page
- Create Edit note page
- Create Delete note page
- Test

- Create Brevo key
- App Brevo api to Strapi
- install brevo sdk
- add brevo helper function
- create routes and controller to share link, unshare and preview
- Update Note collection with `isShared` field boolean
- Update permissions for preview page
- Test email sharelink using curl
- Create Individual note preview page
- Update individual note page with share button and unshare 
- Create share/unshare note page?
- Test
- Create Collaborator collection 
- Create Add editor endpoint
- Create Remove editor endpoint
- Create Find editors endpoint
- Add note access helper to controller
- Update update, find and findOne method in controller 
- Add addEditor, removeEditor, getCollaborators methods in controller
- Test using curl
- Create add Collaborators page
- Create remove Collaborators page
- Add Collaborators list section to individual note page
- Test
- Add request edit route
- Add request edit method to controller
- Test using curl
- Add request edit link to note preview page
- Create Request edit page (Logged in user check)

- Enhance frontend with JavaScript 
- Editor library
- Add Image upload
- Add Pagination
- UI Design Overview
- Add Styling
- Add Theming?

- Backend Deployment to Strapi Cloud
- Frontend Deployment to Cloudflare Pages
- Deployment update strategy?
- Add new auth providers to Strapi (Google, Facebook)
- Version Control

## Parts

- Part 1 is up to 

> *How to add code logic to an already existing long piece of write an abridged pseudocode bigger version of what needs to be written. Show major blocks*

Example

```js
const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
  async find(ctx) {
  //...Leave the same...//
  },
```

- Strapi install - backend
- Create admin
- Login Brevo and create API key
- Add Brevo API key to env var
- Install Brevo SDK
- Add Brevo sender helper
- Update env var with SENDER Email, sender name, frontend app url
- Login strapi
- Create Notes collection
- Fields for Notes collection: title(short text), content(rich text), image(single)
- Create Collaborator collection
- Add public route `sharelink`
<!-- Original article did not add image field to Notes collection, instead it just added the ability to do file uploads that's all-->
