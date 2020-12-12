# Using a Chrome Extension to Crawl URLs

Crawling a list of URLs is a problem I've constantly run into over the years. There are so many ways to do this. Most solutions are server-side, which has pros and cons. If you need to do even remotely complex DOM manipulation, you'll have to do some trickery, usually involving dependencies like Puppeteer. Running the script in the browser is often simpler with fewer dependencies.

Let's start with some boilerplate.

## manifest.json

```
{
  "manifest_version": 2,
  "name": "URL Crawler",
  "version": "0.1",
  "content_scripts": [
    {
      "matches": [
        "https://example.com/*"
      ],
      "js": ["jquery-3.4.1.min.js", "script.js"]
    }
  ]
}
```

In this example, you'll be able to crawl pages on example.com. Copy the the [latest jquery](https://code.jquery.com/) and save it to the root of your extension and make sure the file name matches the string in the `js` array.

## script.js

```
const slugs = ['slug-1', 'slug-2'];
const url = window.location.href;
const slug = url.split('/')[3]; //matches this part of url https://example.com/{MATCH}/not-a-match
const slugIndex = slugs.indexOf(slug);

const nextUrl = () => {
  if (slugIndex <= slugs.length && slugIndex !== -1) {
    let nextSlug = slugs[slugIndex+1];
    let nextUrl = `https://example.com/${nextSlug}`;
    window.location.replace(nextUrl);
  }
}

setTimeout(async () => {
  // Crawling code
  nextUrl();
}, 5000);
```

Start the script by installing an unpacked extension in Chrome then navigate to the first url in the `slugs` array. The crawling code will run, then the next url will automatically load. The process continues until all URLs have been processed.

## Attribution

We've run variations of this script in [Hackernoon](https://hackernoon.com) to clean up markup for stories.
