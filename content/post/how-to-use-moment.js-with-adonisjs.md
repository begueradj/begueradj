---
title: "How to use Moment.js with AdonisJs"
date: 2019-04-19T12:33:21+08:00
categories: ["development"]
draft: false
---


### Preamble

I decided to write this article following a [question](https://forum.adonisjs.com/t/moment-js-in-adonis-framework/3398) published yesterday on the AdonisJs community forum solliciting if there is a tutorial that shows how to use Moment.js in AdonisJs. I answered to that question yesterday, but I would like to share the information with others here.

Do not try to install Moment.js because Adonis.js uses it internally. This means you can take any Moment.js instance and apply on it any Moment.js method you want.

### Server side

We focus only on the server side article, and we will work on the same example the question provides: a **Student** entity. First we create an AdonisJs application in the form of an API (but what follows will also work if you prefer to use the MVC instance of AdonisJs):

```cmd
adonis new server --api-only
```

Create a MySQL database (or choose an other RDBMS if you want):

```cmd
create database test_momentjs
```

Then edit **server/.env** file with the appropriate MySQL credentials. Now we start to create a `students` migration file:

```cmd
adonis make:migration students
```

Open the generated file located in **server/database/migrations** and write this:

```javascript
'use strict'

/** @type {import('@adonisjs/lucid/src/Schema')} */
const Schema = use('Schema')

class StudentsSchema extends Schema {
  up () {
    this.create('students', (table) => {
      table.string('id', 30).primary()
      table.string('name', 30).notNullable()
      table.date('dob').notNullable()
    })
  }

  down () {
    this.drop('students')
  }
}

module.exports = StudentsSchema
```

The field in which we are interested here is the `dob`, acronym for *date of birth*. Next create the `Student` model:

```cmd
adonis make:model Student
```

Go to **server/app/Models/Student.js** and copy paste this code:

```javascript
'use strict'

/** @type {typeof import('@adonisjs/lucid/src/Lucid/Model')} */
const Model = use('Model')

class Student extends Model {

  static get createdAtColumn() {
    return null
  }

  static get updatedAtColumn() {
    return null
  }

  static get dates() {
    return super.dates.concat(['dob'])
  }
  
  static castDates(field, value) {
    if (field === 'dob') {
      return value.format('DD-MM-YYYY')
    }
  }

  getName(name) {
    return name.toLowerCase()
  }
}

module.exports = Student
```

Everything is played on around this model, so let us break it down:

- First, I let AdonisJs know that the corresponding migration file to this model is not implementing the default `created_at` and `updated_at` table columns.
- We let AdonisJs know that `dob` is a date field by concatinating it to the `dates` object within the static `dates()` method:
```javascript
static get dates() {
       return super.dates.concat(['dob'])
}
```
- I ask AdonisJs to display me this date field in the format I am used to at French universities in the static `castDates()` method:

```javascript
static castDates(field, value) {
       if (field === 'dob') {
          return value.format('DD-MM-YYYY')
       }
}
```

Do not worry about the effect of `castDates()`: it has no effect on how the dates are actually stored in the database, all what it does is to format the dates in the shape you want to display it for the client.

Of course we need a controller to handle our HTTP requests and talk with the model we just created above:

```cmd
adonis make:controller Student --type http
```

Open **server/app/Controllers/Http/StudentController.js** and copy paste this code:

```javascript
'use strict'

const Student = use('App/Models/Student')

class StudentController {

  async store({ request, response }) {
    const data = request.post()
    const student = new Student()
    const keys = Object.keys(data)
    keys.forEach((key) => { student[key] = data[key] })
    await student.save()
    response.status(200)
  }

  async show({ response, params }) {
    try {
      const student = await Student.find(params.id)
      return student.toJSON()
    } catch(e) {
      console.log(e.message)
    }
  }
}

module.exports = StudentController
```

Basically we are interested only in saving instances of the Student entity and fetching one of them given his `id` for the client so that we can check if our `dob` is formatted the way we asked AdonisJs to handle it. You can either use `curl` to communicate with this server or use my demo repository I created yesterday for this purpose.

Of course, do not forget to show the HTTP requests where to go to be satisfied when they hit the server. For this, you need to open **server/start/routes.js** file and type this:

```javascript
Route.resource('students', 'StudentController')
  .only(['store', 'show'])
```

This line `.only(['store', 'show'])` means the HTTP requests must be directed only to the `store()` and `show()` methods located in our controller above. Any other HTTP requests not intended to commincate with these methods will fail into the 404 HTTP code.

You can use my GitHub demo and play with the code at your will.

