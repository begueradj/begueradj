---
title: "How to Laravel Sanctum alongside Nuxt.js and Vuetify.js"
date: 2021-05-21T12:33:21+08:00
categories: ["development"]
draft: false
---

### Preamble

AdonisJs documentation shows how to upload files to the server using the HTML5 `<form>` element. But there are cases where axios comes more handy. So let us see how to upload files from a Nuxt.js client application to an AdonisJS RESTful API server with axios. I shared [this project](https://github.com/begueradj/blog/tree/master/adonisjs_rest_api_nuxtjs_file_upload) on my Github profile.
### Project setup

In my demo, the client and server code are set apart. My client application is handled by Nuxt.js:
```cmd
yarn create nuxt-app client
```
and the server by AdonisJs:
```cmd
adonis new server --api-only
```

On my Github project, I use Vuetify.js and Eslint.
### Nuxt.js client code

Here is where the main code is located:
```cmd
components/
├── FileUpload.vue
└── MainPage.vue
pages/
└── index.vue
layouts/
└── default.vue
```

And this is the simple user interface I want to create: 
![file upload](/file_upload.png)

Basically I like to have `MainPage.vue` component to wrap all my other components; in this case I need only the `FileUpload.vue` component:

```javascript
<template>
  <file-upload />
</template>

<script>
import FileUpload from '@/components/FileUpload.vue'

export default {
  name: 'MainPage',
  components: {
    FileUpload
  }
}
</script>
```

The essential part of the code is what the `FileUpload.vue` component contains. So I show here the full code and then will explain the main points:
{{< highlight javascript "linenos=inline">}}
<template>
  <v-container
    grid-list-md
    text-xs-center
    fill-height>
    <v-layout
      row
      wrap
      align-center>
      <v-flex
        xs6
        offset-xs3>
        <v-text-field
          v-model="photoName"
          name="photo"
          outline
          background-color="blue"
          color="blue"
          label="Select image"
          append-icon="attach_file"
          @click="selectImage"/>
        <input
          ref="image"
          class="hide-input"
          type="file"
          accept="image/*"
          @change="imageSelected">
        <v-btn
          class="upload-button"
          color="indigo"
          @click="upload_photo">
          Upload
          <v-icon
            right
            color="white">
            cloud_upload
          </v-icon>
        </v-btn>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  name: 'FileUpload',
  data: () =>({
    photo: '',
    photoName: ''
  }),
  methods: {
    selectImage() {
      this.photo = this.$refs.image.click()
    },
    imageSelected(e) {
      this.$emit('input', e.target.files[0])
      this.photo = this.$refs.image.files[0]
      this.photoName = this.photo.name
    },
    async upload_photo() {
      let formData = new FormData()
      formData.append('file',  this.photo)
      let url = 'http://127.0.0.1:3333/upload'
      let config = {
	headers: {
          'content-type': 'multipart/form-data'
	}
      }
      await this.$axios({
      	method: 'post',
      	url: url,
      	data:  formData,
      	config: config
      })
      
    }
  }
}
</script>

<style scoped>
.hide-input {
    display: none;
}
*{
    text-transform: none !important;
}
.upload-button {
    border-radius: 50px;
    color: white;
}
</style>
{{</ highlight >}}
As Vuetify.js which I am using here (listed in the dependencies of the project) does not have a component which behaves as the HTML5 `<input>` element, I need to hide this later one when displaying the `<v-text-field />` component. This is the traditional simple but efficient trick usually used in this case; then we trigger a click on this hidden file input as follows:
```javascript
imageSelected(e) {
  this.photo = this.$refs.image.files[0]
},
```
My input file element is referenced with `image`, that is why we need to look to the references available in this DOM template with it:
```javascript
<input
  ref="image"
  class="hide-input"
  type="file"
  accept="image/*"
  @change="imageSelected">
```

In my particular case, I am interested in uploading images only, that is why I set `accept="image/*"`. The main thing not to forget in the AJAX request is to declare the content-type header. I think the rest of the code is self explanatory:
```javascript
async upload_photo() {
  let formData = new FormData()
  formData.append('file',  this.photo)
  let url = 'http://127.0.0.1:3333/upload' // This is the endpoint of my REST API on the server 
  // below is equivalent to the enctype="multipart/form-data" we use in the <form> element
  let config = {
    headers: {
      'content-type': 'multipart/form-data'
    }
  }
  await this.$axios({
    method: 'post',
    url: url,
    data:  formData,
    config: config
  })
}
```
If you are a one-liner, the asynchronous code above (the `await` part) can be written concisely as follows:
```javascript
await this.$axios.$post(url, formData, config)
```
### AdonisJs REST API server code

On the server, I first set the endpoint in start/routes.js:
```javascript
Route.post('/upload', 'PhotoController.upload')
```
Then I created the corresponding controller:
```javascript
adonis make:controller PhotoController
```
Inside this controller (`app/Controllers/Http/PhotoController.js`), I received the object `File()` sent by the axios `POST` request in my client code by inspecting the request object of AdonisJs: `const photo = request.file('file')`. Note that I use file to reference its key in the `FormData()` object which contains it. Below I rename the file with the current time of the server’s machine:
```javascript
'use strict'

const Helpers = use('Helpers')

class PhotoController {
  async upload( {request, response} ) {
    const photo = request.file('file')
    await photo.move(Helpers.tmpPath('photos'), {
      name: new Date().getTime() +'.'+avatar.subtype,
      overwrite: true
    })
  }
}

module.exports = PhotoController
```
If you upload a photo, you will find it on the server, precisely in the folder `/tmp/photos`. Note that you have to enable CORS for the requests to be acceped. You can do that by setting **cors: true** in `config/cors.js` file. For larger images, set the limit value of `maxSize` to whatever you want in `/config/bodyParser.js` which is 20mb by default.
