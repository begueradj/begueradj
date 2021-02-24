---
title: "Effective file streaming using AdonisJs"
date: 2019-04-14T12:33:21+08:00
categories: ["development"]
draft: false
---

### Preamble

The majority of upload libraries/frameworks process files multiple times when streaming to an external service such as Amazon S3. Their upload workflows are usually designed like so:

- Process request files then save them to the tmp directory.
- Move each file from the tmp directory to the destination directory.
- Use the external service’s SDK to finally stream the file to the external service.

This process wastes server resources reading/writing single files multiple times.

That is what you can read on the official AdonisJs documentation. Here we learn how to implement that by streaming the files directly to the MySQL database.
### Our stack

- AdonisJs: to build the RESTful API server
- Nuxt.js + Vuetify: to design a user interface to interact with our API.

### Our app

And this is the simple user interface I want to create: 

![file streaming](/adonisjs-file-streaming.png)

The left button will be used to upload a file to the RESTful API server. The right button will be used to download it and display it on the right side

### Project setup

In my demo, the client and server code are set apart. My client application is handled by Nuxt.js:

```cmd
yarn create nuxt-app client
```

and the server by AdonisJs:

```
adonis new server --api-only
```

On my Github project, I use Vuetify.js and Eslint.

### Nuxt.js client code

Here is where the main code is located:

```cmd
components/
├── PhotoUpload.vue
pages/
└── index.vue
layouts/
└── default.vue
```

In **pages/index.vue**, we will have this code:

```javascript
<template>
  <photo-upload />
</template>

<script>
import PhotoUpload from '@/components/PhotoUpload.vue'

export default {
  name: 'MainPage',
  components: {
    PhotoUpload
  }
}
</script>
```

The essential part of the code is what the **PhotoUpload.vue** component contains. So I show here the full code and then will explain the main points: 

```javascript
<template>
  <v-container
    grid-list-md
    text-xs-center
    fill-height
  >
    <v-layout
      row
      wrap
      align-center
    >
      <v-flex
        v-if="url"
        xs12
        sm4
      >
        <span
          class="font-weight-bold"
        >
          Client
        </span>
        <v-img
          :src="url"
          contain
        />
      </v-flex>
      <v-flex
        xs12
        sm4
      >
        <v-text-field
          v-model="photoName"
          name="photo"
          outline
          background-color="blue"
          color="blue"
          label="Select image"
          append-icon="attach_file"
          @click="selectImage"
        />
        <input
          ref="image"
          class="hide-input"
          type="file"
          accept="image/*"
          @change="imageSelected"
        >
        <v-btn
          round
          color="indigo"
          @click="uploadPhoto"
        >
          <v-icon
            color="white"
          >
            cloud_upload
          </v-icon>
        </v-btn>
        <v-btn
          v-if="url"
          color="indigo"
          round
          @click="loadPhotoFromServer"
        >
          <v-icon
            color="white"
            class="download"
          >
            cloud_upload
          </v-icon>
        </v-btn>
      </v-flex>
      <v-flex
        v-if="url"
        xs12
        sm4
      >
        <span
          v-if="photoId"
          class="font-weight-bold"
        >
          Server
        </span>
        <v-img
          v-if="photoId"
          :src="`${$axios.defaults.baseURL}/photos/${photoId}`"
          contain
        />
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  name: 'PhotoUpload',
  data: () => ({
    url: '',
    photo: '',
    photoName: '',
    photoId: 0
  }),
  methods: {

    selectImage() {
      this.photo = this.$refs.image.click()
    },

    imageSelected(e) {
      this.$emit('input', e.target.files[0])
      this.photo = this.$refs.image.files[0]
      this.photoName = this.photo.name
      this.url = URL.createObjectURL(this.photo)
    },

    async uploadPhoto() {
      const data = new FormData()
      data.append('file', this.photo)
      const config = {
        headers: {
          'content-type': 'multipart/form-data'
        }
      }
      await this.$axios.$post('photos', data, config)
    },

    loadPhotoFromServer() {
      this.photoId = 1
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
 .download {
     transform: rotate(180deg)
 }
 </style>
```

Let us break down this mess. Most the above code was explained in the previous article. We added two small aspects to it though. First, we display the image we choose to send to the server. This image preview functionality in Nuxt.js can be useful in many cases you are enough smart to guess. For that, we draw some place holders in the form of Vuetify components to show it up when it is ready:

```javascript
<v-flex
  v-if="url"
  xs12
  sm4
>
  <span
    class="font-weight-bold"
   >
     Client
  </span>
  <v-img
    :src="url"
    contain
  />
</v-flex>
```

As we can see, the image will be displayed only if url is loaded `v-if="url"`. This famous `url` is just a variable we declared in the `data()` method, and we set its content to point to the local photo selected by the user using: `URL.createObjectURL(this.photo)`.

We also added a Vuetify component to display the photo sent back by the server on a button click:

```javascript
<v-flex
  v-if="photoId"
  xs12
  sm4
>
  <span
    class="font-weight-bold"
   >
     Server
  </span>
  <v-img
    :src="${$axios.defaults.baseURL}/photos/${photoId}"
    contain
  />
</v-flex>
```

We set photoId to 1 in `loadPhotoFromServer()` because we are interested to display the file which ID equals to 1 in the MySQL database. We use `$axios.defaults.baseURL` to refer to the `baseURL` key we defined in **nuxt.config.js** file to point to the URL on which our AdonisJs API is being served:

```javascript
<v-btn
  v-if="url"
  color="indigo"
  round
  @click="loadPhotoFromServer"
>
  <v-icon
    color="white"
    class="download"
  >
    cloud_upload
  </v-icon>
</v-btn>
```

### AdonisJs REST API server code

First of all, we need to create the migration, model and controller files for the photos:

```cmd
adonis make:migration photos --action create
adonis make:model Photo
adonis make:controller Photo --type http
```

For our demo, let us be YAGNI and define only what we need in the migration file:

```javascript
'use strict'

/** @type {import('@adonisjs/lucid/src/Schema')} */
const Schema = use('Schema')

class PhotosSchema extends Schema {
  up () {
    this.create('photos', (table) => {
      table.increments()
      table.specificType('file', "longblob").notNullable()
      table.string('type', 10).notNullable()
    })
  }

  down () {
    this.drop('photos')
  }
}

module.exports = PhotosSchema
```

Note that Knex.js which is used by AdonisJs here, does not have a predefined data field for images apart from `binary`. Luckily this same **Knex.js** offers us the possibility to define a data type of our choice as long as the RDBMS we use supports it using `knex.specifiType()` method to define the longblob table column.

Since are not interested in using the timestamps fields for our `photos` table, we should inform the corresponding model **Photo.js** about it:

```javascript
'use strict'

/** @type {typeof import('@adonisjs/lucid/src/Lucid/Model')} */
const Model = use('Model')

class Photo extends Model {
  
  static get createdAtColumn() {
    return null
  }

  static get updatedAtColumn() {
    return null
  }
  
}

module.exports = Photo
```

The important part now comes from the **PhotoController.js** controller:

```javascript
'use strict'

const Photo = use('App/Models/Photo')
// const Database = use('Database')
const getStream = use('get-stream')

class PhotoController {

  async store({ request, response }) {
    let photo = {}
    request.multipart.file('file', {}, async function(file) {
      const fileContent = await getStream.buffer(file.stream)
      photo.filecontents = fileContent
      photo.type = `${file.type}/${file.subtype}`
    })
    
    try {
      await request.multipart.process()
    } catch(e) {
      console.log(e.message)
    }
    
    const photoInstance = new Photo()
    photoInstance.file = photo.filecontents
    photoInstance.type = photo.type
    try {
      await photoInstance.save()
    } catch(e) {
      console.log(e.message)
    }
    response.status(201)
  }

  async show({ request, response, params }) {
    try {
      // const photo = await Database
      //  .table('photos')
      //  .select('file')
      //  .where('id', params.id)
      const photo = await Photo.find(params.id)
      response.header('content-type', `${photo.type}/${photo.subtype}`)
      response.header('content-length', Buffer.byteLength(photo.file))
      // response.send(photo[0].file)
      response.send(photo.file)
    } catch(e) {
      console.log(e.message)
    }
  }
  
}

module.exports = PhotoController
```

We used the `store()` method in the previous article. Here we only add the `show()` method which receives the photoId we mentioned previously as a paramter in order to use to fetch for the photo in the MySQL database. Of course, as for uploading the photo, we need to define the headers properly to let the client know which type of data it is going to handle.

We can use either the **Database** or the **Query Builder** providers to look for the photo in the database. Our code comments out the previous option so that we can use each one we prefer.

If you upload a photo, you will find it on the server, precisely in the folder **/tmp/photos**. Note that you have to enable CORS for the requests to be acceped. You can do that by setting cors: true in **config/cors.js** file. For larger images, set the limit value of maxSize to whatever you want in **/config/bodyParser.js** which is 20mb by default.

Note that we forgot to mention one important thing. Actually it is the first thing to set in the API because without it/them we can not communicate with the server: the routes. So in **start/routes.js**, we can define the routes to our photos resources as follows:

```javascript
Route.resource('photos', 'PhotoController')
```

That is it. I made a [GitHub repository](https://github.com/begueradj/adonisjs-stream-file-to-db) for this demo if anyone wants to try it or make a pull request to improve it.


