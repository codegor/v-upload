# v-upload-base64

> File upload component for Vue.js

<p>
  <a href="https://www.npmjs.com/package/v-upload-base64"><img src="https://img.shields.io/npm/dm/v-upload-base64.svg" alt="Downloads"></a>
  <a href="https://www.npmjs.com/package/v-upload-base64"><img src="https://img.shields.io/npm/v/v-upload-base64.svg" alt="Version"></a>
  <a href="https://www.npmjs.com/package/v-upload-base64"><img src="https://img.shields.io/npm/l/v-upload-base64.svg" alt="License"></a>
</p>

## Installation

### Using yarn

`yarn add v-upload-base64`

### Using npm

`npm i --save v-upload-base64`

## Usage

## Examples

### As component

```html
<template>
  <v-upload multiple v-model="form.file" @input="file" @update="files">
    <template v-slot:default="a">
      <i @click="a.open()" class="cursor-pointer material-icons" >add_circle_outline</i>
    </template>
  </v-upload>
</template>

<script>
import VUpload from 'v-upload-base64';

export default {
  data () {
    return {
      form: {
        file: []
      }
    }
  },
  methods: {
    file (f) {
      console.log(f); // a File object or collection of File objects
    },
    files(f) {
      console.log(f); // a collection of File objects
    }
  }
}
</script>
```

#### Properties

| Name         | Type     | Required | Default   | Info                                                                        |
| -------------| -------- | -------- | ----------| --------------------------------------------------------------------------- |
| **v-model**  | Any      | False    |           |                                                                             |
| **camera**   | Boolean  | False    | False     | If true when open on mobile devise will be open the camera, not chose a file|
| **multiple** | Boolean  | False    | False     | If you want to select multiple file should be true                          |
| **types**    | String   | False    | 'image/*' | Types for inpyt type file                                                   |
| **clear**    | Boolean  | False    | True      | Clear internal container if select another file(s)                          |

#### Events

| Name         | Params                                   | Info                                                                       |
| ------------ | ---------------------------------------- | -------------------------------------------------------------------------- |
| **input**    | _event_: File Object or [File, File,...] | Triggered after File readed from disk              |
| **update**   | _event_: [File, File,...]                | Triggered after File readed from disk              |

## Suggest

For create from base64 img src or show pdf or xls file on new browser window you can use package satellite [vue-small-plugin](https://www.npmjs.com/package/vue-small-plugin)

## License

This project is licensed under
[MIT License](http://en.wikipedia.org/wiki/MIT_License)