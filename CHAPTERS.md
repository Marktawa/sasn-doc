# SECTIONS

1. (From *Title* to *Test Note Delete*) - README1.md
2. (From *User Authentication using ...* to *Test Logout Functionality*) - README2.md
3. (From *Add Sharing: Phase 1* to *Test Add Editor flow*) - README3.md
4. (From *File Upload* to *Test File Upload flow*) - README4.md
5. Rich Text Editor
6. Infinite Scroll
7. Error Handling
8. Security
9. UI Clean up
10. CSS


# CHAPTERS

# Build a Note Sharing app with Social Authentication using Nuxt and Strapi

## Introduction

## Prerequisites

## Create Strapi app

## Basic Overview of a Strapi application?

## Create Notes collection

## Add Permissions for Notes Collection

## Add sample entries for Notes collection

## Create Nuxt frontend app

### Download and Install Nuxt

### Create Hello World Nuxt app

### Create Notes Home page

### Create Notes List page

### Create Individual Note page

### Set up "Create Note" flow

#### Create `create.vue` file

#### Create Nuxt Internal API route for handling create notes

#### Create page to view Note Creation Status

#### Update Notes List page with Create Note link

#### Test Note Creation

### Set up "Update Note" flow

#### Update `pages/notes/[id].vue`

#### Create `edit.vue` file

#### Create Nuxt Internal API route for handling update notes

#### Create `edited.vue` file for confirmation

#### Test Note Update

### Set up "Delete Note" flow

#### Update `page/notes/[id].vue`

#### Create `delete.vue` file

#### Create Nuxt Internal API route for handling delete notes

#### Create `deleted.vue` file for confirmation

#### Test Note Delete

## User Authentication using Social Login Providers: GitHub Authentication

### Register OAuth App in GitHub

### Configuring the GitHub Provider in Strapi

### Configure Public User Permissions

### Configure Authenticated User Permissions

### Get Temporary Code

### Test to see if User is Added to User collection

### Use JWT to Access Protected Endpoints

### Test Token

### Add User from User Permissions Plugin with One to Many relation field

### Add Second GitHub Authenticated User

### Test Token

### Add notes for newly added user

### Test `find`for Authenticated Users

### Override the `find` method in the controller

### Test `find` for Authenticated User

## Add Authentication to Nuxt

### Update Notes Landing page with a Login Button

### Handle OAuth Callback and Store JWT (Server-Side)

#### Update Strapi Redirect URL Configuration

#### Create Server-Side Auth Callback Handler

#### Understanding the Complete Login Flow

#### Test the Server-Side Authentication Flow

### Send Authentication Token with API Requests

#### Update Notes List Page to Include Authentication

#### Create Server Route for Fetching Notes

#### Update Notes List Page

#### How This Works

#### Test Authenticated Notes List

### Update Individual Note Page for Authenticated Requests

#### Create Server Route for Fetching Individual 

#### Update Individual Note Page

#### How This Works

#### Test Authenticated Individual Note View

### Update Create Note Flow for Authenticated Requests

#### Update Create Note Server Handler

#### How This Works

#### Test Authenticated Note Creation

### Update Edit Note Flow for Authenticated Requests

#### Create Server Route for Fetching Note to Edit

#### Update Edit Note Server Handler

#### How This Works

#### Test Authenticated Note Update

### Update Delete Note Flow for Authenticated Requests

#### Update Delete Note Page to Fetch with Authentication

#### Update Delete Note Server Handler

#### How This Works

#### Test Authenticated Note Deletion

### Add Logout Functionality and Display User Information

#### Create Server Route to Get Current User

#### Create Server Route for Logout

#### Update Notes List Page with User Info and Logout

#### Update Individual Note Page with User Info and Logout

#### How the Logout Flow Works

#### Test Logout Functionality

## Add Sharing: Phase 1

## Add Sharing: Phase 2

### Add Email: Configure Brevo

### Add Email: Add A Brevo Service

### Add Email: Test Email Service

#### Create Test Email Route

#### Create Test Email Controller

#### Test the Email Service

#### Troubleshooting

### Create `share` method in Notes controller

#### How the Share Method Works

### Create `share` route

### Test `share` endpoint

### Update Individual Note Page with Share Button

### Create Share Note Page

### Create Nuxt Server Route for Share

#### How This Works

### Create Share Confirmation Page

### Test Note Sharing Flow

## Add Sharing: Phase 3

### Create `request-edit` method in Notes controller

#### How the Request Edit Method Works

### Create `request-edit` route

### Test Request Edit Endpoint

### Update Individual Note Page with Request Edit Button

### Create Request Edit Confirmation Page

### Create Nuxt Server Route for Request Edit

#### How This Works

### Create Request Edit Success Page

### Test Request Edit Flow

## Add Sharing: Phase 4

### Create `addEditor` method in Notes controller

#### How the Add Editor Method Works

### Create `add-editor` route

### Test Add Editor Endpoint

### Update Individual Note Page with Add Editor Button

### Create Add Editor Page

### Create Nuxt Server Route for Add Editor

### Create Add Editor Success Page

### Test Add Editor Flow

## File Upload

### Add Attachment Field to Notes Collection

### Update Public and Authenticated User Permissions for Upload

#### Configure Authenticated User Permissions for Upload

#### Verify Public User Permissions

### Test File Upload Endpoint

#### Test File Upload

#### Test Updating Note with Attachment URL

#### Verify File is Accessible

#### Check Media Library

### Update Individual Note Page to Display Attachment

#### How This Works

### Update Individual Note Page with Attach File Button

### Create File Upload Page

### Create Nuxt Server Route for File Upload

#### How This Works

### Create File Upload Success Page

### Test File Upload Flow