---
title: "Implementing Video Support for Blog Posts in Hugo"
date: 2024-09-06
draft: true
author: Robert Viquez
tags: 
    - Hugo
    - Video
    - Blog
# image: /path/to/image.jpg
# video: /path/to/video.mp4
description: "A guide on how I implemented video support for blog posts using Hugo static website generator."
toc: true
---

## Introduction

In this guide, I'll walk you through the process of implementing video support for blog posts using the Hugo static website generator. This implementation involves modifications to your Hugo configuration and layout files to support displaying videos in both list and single blog post pages.

## Configuration Changes

### `config.yaml`

To start, I updated the `config.yaml` file to manage video settings. Here's how the relevant part of the file looks:

```yaml
title: Recent Posts
enable: true
disableFeaturedImage: false
disableFeaturedVideo: false
socialNetworks:
  github: https://github.com
  linkedin: https://linkedin.com
```

This configuration enables video support and ensures that featured images and videos can be managed for both list and single pages.

## Layout Changes

### List Page Layout (`layouts/_default/list.html`)

I modified the `layouts/_default/list.html` layout to include video support in the list of blog posts. The updated section of the layout file is as follows:

```html
{{ if and (not (.Site.Params.listPages.disableFeaturedImage | default false)) (.Params.image) }}
<div class="card-header">
    <a href="{{ .RelPermalink }}">
        <img src="{{ .Params.image }}" class="card-img-top" alt="{{ .Title }}">
    </a>
</div>
{{ end }}
{{ if and (not (.Site.Params.listPages.disableFeaturedVideo | default true)) (.Params.video) }}
<div class="card-header">
    <a href="{{ .RelPermalink }}">
        <div class="video-container" style="text-align: center;">
            <video width="640" height="360" autoplay loop>
                <source src="{{ .Params.video }}" type="video/mp4" alt="{{ .Title }}">
                Your browser does not support video.
            </video>
        </div>
    </a>
</div>
{{ end }}
<div class="card-body bg-transparent p-4 shadow-sm">
    <a href="{{ .RelPermalink }}" class="primary-font card-title">
        <h5 class="card-title bg-transparent" title="{{ .Title }}">{{ .Title | truncate 25 }}</h5>
```

### Single Page Layout (`layouts/_default/single.html`)

For the single blog post pages, I updated the `layouts/_default/single.html` layout to include video support. The relevant code section is:

```html
{{ if .Params.video }}
<div class="video-container" style="text-align: center;">
    <video width="640" height="360" autoplay loop>
        <source src="{{ .Params.video }}" type="video/mp4" alt="{{ .Title }}">
        Your browser does not support video.
    </video>
</div>
{{ end }}
```

### Footer Layout (`layouts/partials/sections/footer/recentBlogPosts.html`)

I also updated the footer section to include video support for recent blog posts:

```html
{{ if and (not (.Site.Params.footer.recentPosts.disableFeaturedVideo | default false)) (.Params.video) }}
<div class="card-header">
    <div class="video-container" style="text-align: center;">
        <video width="640" height="360" autoplay loop>
            <source src="{{ .Params.video }}" type="video/mp4" alt="{{ .Title }}">
            Your browser does not support video.
        </video>
    </div>
</div>
{{ end }}
<div class="card-body bg-transparent p-3 shadow-sm">
    <a href="{{ .RelPermalink }}" class="primary-font card-title">
        <h5 class="card-title bg-transparent" title="{{ .Title }}">{{ .Title | truncate 25 }}</h5>
```

## CSS Changes

### List Page Styles (`static/css/list.css`)

I updated the CSS for the list page to properly style the video containers:

```css
#list-page .card > .card-header {
    display: contents;
    padding: 0 !important;
    border: none !important;
    background-color: var(--secondary-color) !important;
}

.video-container {
    position: relative;
    padding-top: 56.25%; 
    width: 100%; /* Full width of the parent container */
    max-width: 640px; 
    margin: 0 auto; /* Center the container */
    overflow: auto; /* Hide overflow to maintain aspect ratio */
}

.video-container video {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%; 
    height: 100%; 
    border-radius: 8px; /* Add rounded corners */
}
```

### Single Page Styles (`static/css/single.css`)

For the single page layout, I updated the CSS to center and style the video container:

```css
#single .video-container {
    justify-content: center; 
    align-items: center; 
    display: flex; 
}

#single .video-container video {
    align-self: center;
    border-radius: 16px; 
    display: block;
}

#single .featured-image {
    padding: 1rem;
    padding-top: 0;
    border: 1px solid var(--secondary-color);
    opacity: 0.9;
}
```

## Conclusion

With these changes, your Hugo site will now support displaying videos in various sections, including the list of blog posts, single blog post pages, and recent posts in the footer. Make sure to test the implementation thoroughly to ensure everything works as expected.
