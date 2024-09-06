---
title: "Netlify Deployment with Github Private Submodule"
date: 2024-09-05T23:51:35-06:00
draft: false
author: "Robert Viquez"
tags:
    - Netlify
    - Github Submodules
    - Hugo
video: "https://res.cloudinary.com/dqyllgamr/video/upload/v1725597353/netlify_github_hugo_i1cnb9.mp4"
description: "Guide to deploy a website on Netlify with a Github repository that includes private submodules. Learn how to handle authentication issues."
toc: true

---
---


While deploying a website to Netlify using a GitHub repository as the source, I ran into a deployment issue. The error log looked something like this:


```vb
   Failed during stage 'preparing repo': Error checking out submodules: Submodule 'themes/your-theme' (https://github.com/your_username/your-theme.git) registered for path 'themes/your-theme'
   Error checking out submodules: Submodule 'themes/your-theme' (https://github.com/your_username/your-theme.git) registered for path 'themes/your-theme'
   Cloning into '/opt/build/repo/themes/your-theme'...
   fatal: clone of 'https://github.com/your_username/your-theme.git' into submodule path '/opt/build/repo/themes/your-theme' failed
```


After going through [GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) and [Netlify](https://docs.netlify.com/git/repo-permissions-linking/#access-other-repositories-at-build) docs, found out that the problem was a private Github repository cloned as a [submodule](https://github.blog/open-source/git/working-with-submodules/) in my main project. I had cloned a [Hugo](https://github.com/gohugoio/hugo) theme into my themes folder as a submodule, and even though making the repo public could have solved the issue, I wanted to keep it private.

<div class="row">
   <strong>Why not just clone it normally?</strong>
</div>
<div style="margin-bottom: 20px;"></div>

   Well, using a submodule to manage the theme allows you to update and maintain the theme separately from your main project. Which is nice.

   This problem often arises when working with private repositories or [SSH-based URLs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding). To resolve this issue, you need to provide Netlify with the correct credentials or **SSH Keys**, especially if your website relies on private repositories as submodules.

---

## Deploying on Netlify 

<div class="row">
   <strong> 1. Prepare Your Website</strong>
</div>

   Make sure your website is created. For example, I'm using [Hugo](https://github.com/gohugoio/hugo) and a fork of the [hugo-profile theme](https://github.com/gurusabarish/hugo-profile).


<div class="row">
   <strong> 2. Sign Up or Log In to Netlify</strong>
</div>

   Go to [Netlify](https://www.netlify.com/) and sign up or log in.

<div class="row">
   <strong> 3. Add a New Site</strong>
</div>

   In the Netlify dashboard, click on the “**Add new site**” button.

   ![Add a New Site](https://res.cloudinary.com/dqyllgamr/image/upload/v1725592428/Screenshot_2024-09-05_211237_nxuxez.png)

---


## Deploy on Netlify From Github 

<div class="row">
   <strong> 4. Import an Existing Project from Github</strong>
</div>

   Sign in to your Github account (*if you haven't already*) and select your website's repository. It will try to deploy right away.

<div class="row">
   <strong> 5. Configure Build Settings</strong>
</div>

   In your **Site configuration > Build & Deploy**. In the **Build Settings** set the **Build command** to:

   ```bash
   git submodule update --init --recursive && hugo #change hugo for whatever your build command is
   ```

   And set the **Publish directory** to `public`.

   In **Build & deploy settings > Branches and deploy contexts**, select your **Production branch**, such as `master`.

---

### Use Private Submodules

<div class="row">
   <strong> 6. Configure Your Deploy Key </strong>
</div>
<div style="margin-bottom: 20px;"></div>

   If your repository uses a private [Github submodule](https://docs.github.com/en/pages/getting-started-with-github-pages/using-submodules-with-github-pages), you'll need to add a **deploy key**.
   To do that (*in Netlify*) go to **Site configurations > Build & Deploy settings > Deploy Key** and hit on **Generate public deploy key**.

   ![Deploy Key Settings](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591352/Pasted_image_20240903201732_ps3bep.png)

   This will generate a public and private **SSH Key**. 
   
   Now, go to Github and [sign in](https://github.com/login).

   Go to the private repo of your submodule and in **Settings > Deploy keys**, click on **Add deploy key** in the top-right corner.

   ![Add Deploy Key](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591356/Pasted_image_20240903202710_dvnlkf.png)

   ![Deploy Key Form](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591360/Pasted_image_20240903202826_bbdwsg.png)

   ![Add Deploy Key Button](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591362/Pasted_image_20240903202849_q5ctay.png)
   
   Add a descriptive title, such as `GIT_YOUR-SITE-NAME_KEY`, paste your Netlify deploy key in the Key section, and select **Allow write access** if necessary. Click **Add key**.

   ![Add new deploy key](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591349/Pasted_image_20240903203122_zlipte.png)

<div style="margin-bottom: 40px;"></div>

#### Add SSH Key to Netlify

   In the Netlify dashboard, under **Site configuration > Environment variables**, add a new variable. 
   
   Name it something descriptive, like `GIT_REPO_SSH_KEY`, and paste the same deploy key you used in the deploy key settings. Click **Create variable**.

   ![Add Environment Variable](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591350/Pasted_image_20240903203543_aeqcmy.png)

<div style="margin-bottom: 20px;"></div>

#### Update `.gitmodules` 

   In your source code, go to your `.gitmodules` file and modify it to use the SSH URL:

   ```ini
   [submodule "themes/your-theme"]
     path = themes/your-theme
     url = git@github.com:yourusername/your-theme.git
   ```

   After making these changes, push them to your repository and trigger a new build on Netlify (*if not done automatically*). 

   The build should now be able to authenticate and pull the private submodule.

   >*Note: Ensure that the SSH key is unique for each repository to avoid conflicts.*

## Notes

> #### [Git Submodule Limitations](https://docs.netlify.com/git/repo-permissions-linking/#git-submodules)
>
> - **Recursive Submodules**: Netlify doesn’t support recursive submodules. If your submodule has its own submodules, Netlify won’t detect and clone them. Workarounds may cause unintended failures.
> - **Client-side Git Hooks**: Netlify doesn’t support client-side Git hooks and won’t run Git hooks from a submodule.

> #### [Github Deploy Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys)
>
> **Pros**:
> - Deploy keys allow access to the repository and server for deployments.
> - Users don’t need to change local SSH settings.
> - Deploy keys are read-only by default but can be given write access.
>
> **Cons**:
> - Deploy keys grant access to a single repository only.
> - They are usually not protected by a passphrase.
> - If the user who created the deploy key is removed, the key remains active.

---

## References

- [Hugo](https://github.com/gohugoio/hugo) - A fast and flexible static site generator.
- [hugo-profile](https://github.com/gurusabarish/hugo-profile) - The Hugo theme used in this guide.
- [Netlify](https://www.netlify.com/) - Cloud platform for web hosting and serverless functions.
- [Using Submodules with Github Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-submodules-with-github-pages) - Official Github documentation on submodules.
- [Git Submodule Limitations on Netlify](https://docs.netlify.com/git/repo-permissions-linking/#git-submodules) - Documentation on handling Git submodules on Netlify.
- [Github Deploy Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys) - Guide on managing SSH keys for Github repositories.

---
