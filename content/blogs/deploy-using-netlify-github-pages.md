---
title = 'Deploy a Site Using Netlify and GitHub with Private Submodules'
date = 2024-09-05T20:37:05-06:00
draft = false
tags = [Netlify, GitHub Submodules, Hugo]
categories = [Web Development]
author = "Robert Viquez"
description = "Guide to deploy a website on Netlify with a GitHub repository that includes private submodules. Learn how to handle authentication issues."
featured_image = ""
---

# Deploy a Site Using Netlify and GitHub with Submodules

If you're encountering errors when deploying to Netlify, particularly issues with authentication of private submodule repositories during the build process, keep reading.

### Example of the Error Log

```vb
7:47:40 PM: Failed during stage 'preparing repo': Error checking out submodules: Submodule 'themes/your-theme' (https://github.com/your_username/your-theme.git) registered for path 'themes/your-theme'
Cloning into '/opt/build/repo/themes/your-theme'...
fatal: could not read Username for 'https://github.com': No such device or address
fatal: clone of 'https://github.com/your_username/your-theme.git' into submodule path '/opt/build/repo/themes/your-theme' failed
Failed to clone 'themes/your-theme'. Retry scheduled
Cloning into '/opt/build/repo/themes/your-theme'...
fatal: could not read Username for 'https://github.com': No such device or address
fatal: clone of 'https://github.com/your_username/your-theme.git' into submodule path '/opt/build/repo/themes/your-theme' failed
Failed to clone 'themes/your-theme' a second time, aborting
: exit status 1
7:47:40 PM: Error checking out submodules: Submodule 'themes/your-theme' (https://github.com/your_username/your-theme.git) registered for path 'themes/your-theme'
Cloning into '/opt/build/repo/themes/your-theme'...
fatal: could not read Username for 'https://github.com': No such device or address
fatal: clone of 'https://github.com/your_username/your-theme.git' into submodule path '/opt/build/repo/themes/your-theme' failed
Failed to clone 'themes/your-theme'. Retry scheduled
Cloning into '/opt/build/repo/themes/your-theme'...
fatal: could not read Username for 'https://github.com': No such device or address
fatal: clone of 'https://github.com/your_username/your-theme.git' into submodule path '/opt/build/repo/themes/your-theme' failed
Failed to clone 'themes/your-theme' a second time, aborting
```

This problem often arises when working with private repositories or SSH-based URLs. To resolve this issue, you need to provide Netlify with the correct credentials or SSH keys, especially if your website, such as a Hugo site using a theme as a submodule, relies on private submodules.

### Steps to Deploy a Website on Netlify from a GitHub Repository with a Private Submodule

1. **Prepare Your Website**

   Make sure your website is created. For example, I'm using [Hugo](https://github.com/gohugoio/hugo) and a fork of the [hugo-profile theme](https://github.com/gurusabarish/hugo-profile).


2. **Sign Up or Log In to Netlify**

   Go to [Netlify](https://www.netlify.com/) and sign up or log in.


3. **Add a New Site**

   In the Netlify dashboard, click on the “**Add new site**” button.

   ![Add a New Site](https://res.cloudinary.com/dqyllgamr/image/upload/v1725592428/Screenshot_2024-09-05_211237_nxuxez.png)

4. **Import an Existing Project from GitHub**

   Sign in to your GitHub account (*if you haven't already*) and select your website's repository. It will try to deploy right away.

5. **Configure Build Settings**

   In your **Site configuration** menu, go to **Build & Deploy**. In the **Build Settings** set the **Build command** to:

   ```bash
   git submodule update --init --recursive && hugo #change hugo for whatever your build command is
   ```

   And set the **Publish directory** to `public`.

   In **Build & deploy settings > Branches and deploy contexts**, select your **Production branch**, such as `master`.


6. **Configure Your Deploy Key**

   If your repository uses a private [Github submodule](https://docs.github.com/en/pages/getting-started-with-github-pages/using-submodules-with-github-pages), you'll need to add a **deploy key**. 
   
   To do that (*in Netlify*) go to **Site configurations > Build & Deploy settings > Deploy Key**.

   ![Deploy Key Settings](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591352/Pasted_image_20240903201732_ps3bep.png)

   This will generate a public and private **SSH Key**. 
   
   Now, go to GitHub and [sign in](https://github.com/login).

   Go to the private repo of your submodule and in Settings > Deploy keys, click on `Add deploy key` in the top-right corner.

   ![Add Deploy Key](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591356/Pasted_image_20240903202710_dvnlkf.png)
   ![Deploy Key Form]([Pasted%20image%2020240903202826.png](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591360/Pasted_image_20240903202826_bbdwsg.png))

   Add a descriptive title, such as `GIT_YOUR-SITE-NAME_KEY`, paste your Netlify deploy key in the Key section, and select **Allow write access** if necessary. Click **Add key**.

7. **Add SSH Key to Netlify**

   In the Netlify dashboard, under **Site configuration > Environment variables**, add a new variable. 
   
   Name it something descriptive, like `GIT_REPO_SSH_KEY`, and paste the same deploy key you used in the deploy key settings. Click **Create variable**.

   ![Add Environment Variable](https://res.cloudinary.com/dqyllgamr/image/upload/v1725591350/Pasted_image_20240903203543_aeqcmy.png)

8. **Update `.gitmodules` File**

   In your source code, go to your `.gitmodules` file and modify it to use the SSH URL:

   ```ini
   [submodule "themes/your-theme"]
     path = themes/your-theme
     url = git@github.com:yourusername/your-theme.git
   ```

   After making these changes, push them to your repository and trigger a new build on Netlify (*if not done automatically*). 

   The build should now be able to authenticate and pull the private submodule.

   >*Note: Ensure that the SSH key is unique for each repository to avoid conflicts.*

### Additional Notes

> #### [Git Submodule Limitations](https://docs.netlify.com/git/repo-permissions-linking/#git-submodules)
>
> - **Recursive Submodules**: Netlify doesn’t support recursive submodules. If your submodule has its own submodules, Netlify won’t detect and clone them. Workarounds may cause unintended failures.
> - **Client-side Git Hooks**: Netlify doesn’t support client-side Git hooks and won’t run Git hooks from a submodule.

> #### [GitHub Deploy Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys)
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
- [Using Submodules with GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-submodules-with-github-pages) - Official GitHub documentation on submodules.
- [Git Submodule Limitations on Netlify](https://docs.netlify.com/git/repo-permissions-linking/#git-submodules) - Documentation on handling Git submodules on Netlify.
- [GitHub Deploy Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys) - Guide on managing SSH keys for GitHub repositories.

---
