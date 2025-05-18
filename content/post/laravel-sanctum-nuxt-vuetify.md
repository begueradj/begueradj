---
title: "Laravel Sanctum with Nuxt and Vuetify SPA"
date: 2021-08-07T12:33:21+08:00
categories: ["development"]
draft: false
---

Sanctum allows, among other things, to authenticate an SPA via cookies using the `web` authentication guard. The following discussion shows how to create a single page application using Nuxt.js and VuetifyJs to connect it (sign up, sign in and logout a user) to a backend API through Laravel Sanctum. Laravel 8 is the version used below.


## 1. Backend

Below creates a new Laravel project named 'server':
```cmd
composer create-project --prefer-dist laravel/laravel server
```

Once the database settings are done in `server/.env` file, migrations can be run:
```cmd
php artisan migrate
```

At this point, Laravel Sanctum can be installed and set as per the official documentation:
```cmd
# Install Sanctum
composer require laravel/sanctum

# Publish its configuration and migration files 
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

# Run Sanctum migrations
php artisan migrate

# Add Sanctum's middleware to API middleware group in server/app/Http/Kernel.php file
'api' => [
    \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

More serious things start at this point:
In **routes/api.php**, `auth:sanctum` should be used instead of the default `auth:api`:

```php
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```

The above route `api/user` is used to request the identity of the currently logged in user, i.d. after the authentication is successful.

In **.env** these 2 values have to be added:

```cmd
# Used in config/session.php
SESSION_DOMAIN=localhost

# Used in config/sanctum.php. This should match our client
SANCTUM_STATEFUL_DOMAINS=localhost:3000
```

Browsers do not allow HTTP requests between different domains. With CORS, a server can agree about which client it can receive requests. This is done in Laravel in **config/cors.php** file by setting the following key to `true`:

```php
'supports_credentials' => true,
```

It would be nice to create a user in the database at this stage. This can be done from the command line quite fast:
```cmd
php artisan tinker
Psy Shell v0.10.8 (PHP 7.4.22 — cli) by Justin Hileman
>>> $user = new App\Models\User;
=> App\Models\User {#3391}
>>> $user->name = 'begueradj';
=> "begueradj"
>>> $user->email = 'billal@begueradj.com';
=> "begueradj@gmail.com"
>>> $user->password = bcrypt('begueradj');
=> "$2y$10$vXdKSDE9Qud/cy4urMrRieeUED2kNSnmZLGfMx3xZls7.69FtFCUe"
>>> $user->save();
=> true
```

Next, 2 controllers can be created to login and to register a user:

### 1.1. LoginController

```cmd
php artisan make:controller LoginController
```

The below code is not perfect and does not comply with best practices but the goal is to just make things function:
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Auth;

class LoginController extends Controller
{
    public function login(Request $request)
    {
        $request->validate([
            'email' => ['required'],
            'password' => ['required']
        ]);

        if (Auth::attempt($request->only('email', 'password'))) {
            return response()->json(Auth::user(), 200);
        }

        /* throw ValidationException::withMessages([
            'email' => ['The provided credentials are incorrect.']
        ]); */
    }

    public function logout()
    {
        Auth::logout();
    }
}
```

### 1.2 RegisterController
It could be done this way:
```
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Auth;

class RegisterController extends Controller
{
    public function register(Request $request)
    {
        // var_dump($request->name);
        $request->validate([
             'name' => ['required'],
             'email' => ['required', 'email', 'unique:users'],
             'password' => ['required', 'min:8', 'confirmed']
         ]);

         User::create([
             'name' => $request->name,
             'email' => $request->email,
             'password' => Hash::make($request->password)
         ]);
    }
}
```

The new functions can be reached out through  **routes/api.php**:

```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

use App\Http\Controllers\LoginController;
use App\Http\Controllers\RegisterController;

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

Route::post('/login', [LoginController::class, 'login']);
Route::post('/register', [RegisterController::class, 'register']);
Route::post('/logout', [LoginController::class, 'logout']);
```

## 2. Frontend

A SPA can be created using Nuxt.js. Below, VuetifyJs as well as axios are going to be used during the creation process of the client project:

```cmd
yarn create nuxt-app client
```

The **@nuxtjs/auth-next** module can be installed with:

```cmd
yarn add --exact @nuxtjs/auth-next
```

Both **@nuxtjs/auth-next** and **@nuxtjs/axios** should be declared in **nuxt.config.js**:

```javascript
modules: [
  '@nuxtjs/axios',
  '@nuxtjs/auth-next'
]
```

The settings for both modules are as follows:

```javascript
axios: {
  /**
    When issuing a request to baseURL that needs to pass authentication headers to
    the backend, 'credentials' should be set to 'true'
  */
  credentials: true, // default value of withCredentials is fale
  
  // This is where to hit the server
  baseUrl: 'http://localhost:8000'
},
```

And:


{{< highlight javascript "linenos=inline">}}
auth: {
  redirect: {
    login: '/login',
    logout: '/',
    callback: '/login',
    home: '/'
  },
  strategies: {
    laravelSanctum: {
        provider: 'laravel/sanctum',
        url: 'http://localhost:8000',
        endpoints: {
          login: { url: '/api/login', method: 'post' }
        },
        user: {
          property: false
        },
        tokenRequired: false,
        tokenType: false
      }
  },
  localStorage: false
},
{{</ highlight >}}

Lines 2 - 7 can be removed as that's already the default behavior of nuxt auth package.

If the project is mainly admin focused with only few public pages, the authenticaion module can be set globally:
```javascript
router: {
  middleware: ['auth']
},
```

And later it can be disabled on indidual public pages (routes, to be exact) as follows:
```javascript
export default {
  auth: false
}
```

### 2.1. Login component and route

Login component can be designed as follows:
{{< highlight javascript "linenos=inline">}}
<template>
  <v-container fill-height>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6">
        <v-form ref="form">
          <v-text-field
            v-model="form.email"
            :counter="10"
            label="Email"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              email
            </v-icon>
          </v-text-field>

          <v-text-field
            v-model="form.password"
            label="Password"
            type="password"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              lock
            </v-icon>
          </v-text-field>

          <v-btn color="blue-grey" class="ml-0" @click="login">
            Login
          </v-btn>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      form: {
        email: '',
        password: ''
      }
    }
  },

  methods: {
    async login () {
       // this is managed automatically in the background
      // await this.$axios.$get('/sanctum/csrf-cookie')
      try {
        let response = await this.$auth.loginWith('laravelSanctum', {
          data: this.form
        })
        // console.log(response)
        this.$router.push('/dashboard')
      } catch (err) {
        console.log(err)
      }
    }
  }
}
</script>
{{</ highlight >}}

Hence this component can be used in the **login** route:
```javascript
<template>
  <login />
</template>
<script>
import Login from '~/components/authentication/Login.vue'
export default {
  components: {
    Login
  }
}
</script>
```

The output should look as follows:

![file upload](login_route.png)


### 2.2. Register component and route

The component should provide a user interface to populate the user migration file of the backend server:
{{< highlight javascript "linenos=inline">}}
<template>
  <v-container fill-height>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6">
        <v-form ref="form">
          <v-text-field
            v-model="form.name"
            :counter="10"
            label="Name"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              account_circle
            </v-icon>
          </v-text-field>

          <v-text-field
            v-model="form.email"
            :counter="10"
            label="Email"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              email
            </v-icon>
          </v-text-field>

          <v-text-field
            v-model="form.password"
            label="Password"
            type="password"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              lock
            </v-icon>
          </v-text-field>

          <v-text-field
            v-model="form.password_confirmation"
            label="Confirm password"
            type="password"
            color="green"
            required
          >
            <v-icon slot="prepend" color="grey">
              lock
            </v-icon>
          </v-text-field>

          <v-btn color="blue-grey" class="ml-0" @click="login">
            Register
          </v-btn>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Register',
  data () {
    return {
      form: {
        name: '',
        email: '',
        password: '',
        password_confirmation: ''
      },
      errors: {}
    }
  },

  methods: {
    login () {
      this.$axios
        .$post('/api/register', this.form)
        .then(function (response) {
          console.log(response)
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  }
}
</script>
{{</ highlight >}}

The **register** route can use the above component:
```javascript
<template>
  <register />
</template>
<script>
import Register from '~/components/authentication/Register.vue'
export default {
  auth: false,
  components: {
    Register
  }
}
</script>
```

Hitting the **register** route would result in this user interface:

![file upload](register_route.png)

### 2.3. Authenticated user

For the authenticate user, a **Dashboard** component could be created.

{{< highlight javascript "linenos=inline">}}
<template>
  <v-container fill-height>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6">
        Hello
        <span v-if="user"> {{ user.name }}</span>
      </v-col>
    </v-row>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6">
        <v-btn @click="logout">
          Logout
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Welcome',
  data () {
    return {
      user: ''
    }
  },
  created () {
    this.getAuthenticatedUser()
  },

  methods: {
    async getAuthenticatedUser () {
      console.log('loggedIn : ' + this.$auth.loggedIn)
      try {
        let response = await this.$axios.$get('/api/user')
        this.user = response
        console.log(response.name)
      } catch (err) {
        console.log(err)
      }
    },
    async logout () {
      console.log('logout')
      await this.$axios.$post('/api/logout')
      this.$router.push('/')
    }
  }
}
</script>
{{</ highlight >}}

Line 34 requests the `/api/user` route in **server/routes/api.php** declared earlier and which is guarded by Sanctum.

This component displays the name of the user who is signed in successfully to the application, and offers a logout button to click and exit.

The corresponding **dashboard** route can use the above component as follows:
```javascript
<template>
  <v-container fill-height>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="6">
        <welcome />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Welcome from '~/components/Welcome.vue'
export default {
  name: 'Dashboard',
  components: {
    Welcome
  }
}
</script>
```



