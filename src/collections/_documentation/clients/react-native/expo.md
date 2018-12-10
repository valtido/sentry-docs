---
title: 'Using Sentry with Expo'
---

[Expo](https://expo.io/) is an awesome way to quickly create and play around with your React Native app. Now you can also use Sentry with Expo, which is pretty simple todo:

```bash
$ npm i sentry-expo --save
```

&nbsp;

In your `main.js` or `app.js`:

```javascript
import Sentry from 'sentry-expo';
// import { SentrySeverity, SentryLog } from 'react-native-sentry';
Sentry.config('___PUBLIC_DSN___').install();
```

Note that for Expo, you have to use you public DSN instead of the private one. This is due to Expo not using the native integration, yet. This could change in future releases.

&nbsp;

For uploading sourcemaps, you have to add this to your `exp.json` or `app.json`:

```javascript
{
  // ... your existing exp.json configuration is here

  "hooks": {
    "postPublish": [
      {
        "file": "sentry-expo/upload-sourcemaps",
        "config": {
          "organization": "your team short name here",
          "project": "your project short name here",
          "authToken": "your auth token here"
        }
      }
    ]
  }
  // ...
}
```

If you need more help, you can checkout the docs directly on [Expo’s docs page](https://docs.expo.io/versions/latest/guides/using-sentry.html#content)
