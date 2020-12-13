# Initializing Firebase in a Chrome Extension

This is deceptively tricky. Permission considerations make it more challenging to use Firebase in a Chrome extension. This is a very minimal config.

## manifest.json

```
{
  "manifest_version": 2,
  "name": "Project Name",
  "version": "0.1",
  "content_scripts": [
    {
      "content_security_policy":"script-src 'self' https://www.gstatic.com/ https://*.firebaseio.com https://www.googleapis.com; object-src 'self'",
      "matches": [
        "https://example.com/*"
      ],
      "js": ["firebase-app.js", "firebase-firestore.js", "content.js"]
    }
  ]
}
```

## content.js

```
const config = {
  apiKey: "",
  databaseURL: "",
  projectId: "",
  messagingSenderId: ""
};
firebase.initializeApp(config);
```

## Save these files in the root of your extension

- firebase-app.js ([CDN](https://www.gstatic.com/firebasejs/8.1.2/firebase-app.js))
- firebase-firestore.js ([CDN](https://www.gstatic.com/firebasejs/8.1.2/firebase-firestore.js))

## Use Cases

[Hackernoon](https://hackernoon.com) - We need to optimize the performance of images in our library. In order to do this, we need to iterate through thousands of previously published stories and measure the width and height of each image in the story. By knowing the width and height, we can take advantage of [lazy loading](https://developer.mozilla.org/en-US/docs/Web/Performance/Lazy_loading) to initially load the story pages without images. There are certainly a lot of solutions to this problem but some of the pure backend solutions using Puppeteer and Node.js have been problematic. So, I've written scripts to measure images from the client side using a Chrome Extension. It's a hack, but it works.
