# Primevue Uploadcare File Uploader

This is an element for [weweb.io](https://www.weweb.io/). It allows uploading files (images) to [Uploadcare](https://uploadcare.com/) and applying transformations to them.

## Installation

To run locally, first install all dependencies with `npm i`

## Start

To serve locally, run `npm run serve --port=[PORT]`, and then go to Weweb editor, open developper popup and add your custom element.

## Build

Before release, you can check build error by running `npm run build --name=my-element`

## Releases

### 0.0.7

- all features from 0.0.6 can be disabled/enabled in Weweb
- fix bug stopping the dialog from closing
- emit single-object event for each image

### 0.0.6 - First round of transformations, Uploadcare only (no Weweb integration), text overlay missing

- grayscale transformation, Uploadcare only (no Weweb integration)
- smart resize transformation, Uploadcare only (no Weweb integration)
- blur transformation, Uploadcare only (no Weweb integration)

### 0.0.5

- basic watermark transformation, Uploadcare only (no Weweb integration)
  - with opacity
- basic auto format transformation, Uploadcare only (no Weweb integration)
- basic quality transformation, Uploadcare only (no Weweb integration)
- basic profile picture preset transformation, Uploadcare only (no Weweb integration)
- basic preview transformation, Uploadcare only (no Weweb integration)

### 0.0.4

- show the link
- smart scale crop transformation, Uploadcare only (no Weweb integration)
- basic rotation transformation, Uploadcare only (no Weweb integration)

### 0.0.3

- fix buttons' icons not being centered
- allow removing files from the file picker
- add 'download' and 'copy link' buttons in the file modal

### 0.0.1

Initial release

## Requirements

- Has these capabilities, in the UI:

  - [x] scale_crop with smart (they can enter the dimensions)
  - [x] rotate
  - [x] watermark (they can enter another UUID)
  - [x] format
  - [x] quality
  - [x] profile picture preset
  - [x] preview
  - [x] grayscale
  - [x] smart resize
  - [ ] ~~overlay (text and image only)~~
  - [x] blur

- The capabilities can be enabled/disabled in Weweb:

  - [x] scale_crop with smart
  - [x] rotate
  - [x] watermark
  - [x] format
  - [x] quality
  - [x] profile picture preset
  - [x] preview
  - [x] grayscale
  - [x] smart resize
  - [ ] ~~overlay (text and image only)~~
  - [x] blur

- Emits an array of `{ uuid (str), cdnURL, size (# decimal), fileName (str), moderationCheck (bool) }` for each image
  - [x] single object event for each image
