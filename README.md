# Build a Note Sharing app with Social Authentication using Nuxt and Strapi

## Introduction

- Description of article
- Feature overview/list
- Demo video
- Links to demo video, GitHub repo, live link

## Prerequisites

- Knowledge of Strapi (Link to Quickstart), (Link to What is Strapi)
- Knowledge of HTML, CSS, JavaScript
- Node
- GitHub
- Facebook
- Google?
- Strapi Cloud
- Brevo

## Steps to be taken?

- Note taking system > Auth > Note Sharing system

## Create Strapi app

```shell
npx create-strapi-app@latest backend \
 --skip-cloud \
 --skip-db \
 --no-example \
 --js \
 --use-npm \
 --install \
 --no-git-init
```

```shell
cd backend
```

Create admin
```shell
npm run strapi admin:create-user -- --firstname=Kai --lastname=Doe --email=chef@strapi.io --password=Gourmet1234
```

Start your Strapi server.
```shell
npm run develop
```

Open browser and visit the admin page at `http://localhost:1337/admin`. Login and view your dashboard.

![Strapi Admin Dashboard]()

## Basic Overview of a Strapi application?

- Admin Panel Dashboard
- Content Manager
- Content-Type Builder
- Media Library
- Settings
- Marketplace
- Project File Tree Structure
- Links to Project Structure and Overview of Strapi Dashboard

## Create Notes collection

- Content-Type Builder
- Notes collection with title(short text), content(long text) fields

## Add Permissions for Notes Collection
- Add Public user permissions for Notes (find, findOne, create, delete, update)

## Add sample entries for Notes collection

- Visit the Content Manager and add sample notes to the Notes collection

- Test `find`
- C


`findOne`, `create`, `delete`, `update`
