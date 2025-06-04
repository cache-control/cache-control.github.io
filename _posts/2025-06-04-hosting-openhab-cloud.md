---
title: "Hosting openHAB Cloud"
date: 2025-06-04
---
## Introduction
This post shows how to setup push messaging in openHAB Cloud. It will
explain where to obtain `senderId` and `serviceFile` for the following
configuration in `config.json`.

```
    "gcm" : {
      "senderId": "1234567890",
      "serviceFile" : "serviceAccountKey.json"
    },
```

## Getting started

Clone and follow the clear instructions provided at
`https://github.com/openhab/openhab-cloud`

It is important to use the same Node.js version.  At the time of this
writting, the repo has this verbiage.

```
If you see the node version, you are fine to continue (Note: openHAB Cloud is based on Node.js version 18).
```

## Create new gcm project

* Log in to `https://console.cloud.google.com/welcome`
* Near top-left, click on "Open project picker (Ctrl O)" to open modal
* Click "New Project"
    * Provide "Project Name"
    * Click "Create"

## Get `senderId`

The value for `senderId` is `Project number:`, which is provided on the welcome page.
`https://console.cloud.google.com/welcome`

## Download `serviceAccountKey.json`

* Click `Settings and Utilities` icon (3 vertical dots), top-right.
    * Select `Project Settings`
* Select `Service Accounts` from left menu
    * Select `Keys` tab
    * Click `Add key`
