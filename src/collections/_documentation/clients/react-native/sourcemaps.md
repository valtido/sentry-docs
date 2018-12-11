---
title: 'Sourcemaps for Other Platforms'
---

Currently, automatic sourcemap handling is only implemented for iOS with Xcode and Android with Gradle. However, if you manually invoke the [react-native packager](https://github.com/facebook/metro), you can get sourcemaps anyways by passing _–sourcemap-output_ to it.

If you do get sourcemaps, you can upload them with `sentry-cli`. Make sure to pass `--rewrite` to the `upload-sourcemaps` command, which will fix up the sourcemaps before upload (inlines sources, etc.).

&nbsp;
#### Example of -sourcemap-output

```bash
react-native bundle \
  --dev false \
  --platform android \
  --entry-file index.android.js \
  --bundle-output android.main.bundle \
  --sourcemap-output android.main.bundle.map
```

&nbsp;

To then upload, use this:

```bash
node_modules/@sentry/cli/bin/sentry-cli releases \
    files RELEASE_NAME \
    upload-sourcemaps \
    --dist DISTRIBUTION_NAME \
    --strip-prefix /path/to/project/root \
    --rewrite /path/to/your/files
```

&nbsp;
#### Values for `RELEASE_NAME` and `DISTRIBUTION_NAME`

`RELEASE_NAME`:

: The bundle ID or package name (reverse DNS notation of your app) followed by a dash and the human readable version name that is set for your release. For instance: `com.example.myapp-1.0`.

&nbsp;
`DISTRIBUTION_NAME`:

: This is the version code or build ID, depending on your platform. For instance, just set this to whatever is set in your _Info.plist_, or what your Gradle setup generates (eg: `52`).
