---
title: "Nuxt.js deployment on Gitlab"
date: 2018-11-18T12:33:21+08:00
categories: ["development"]
draft: false
---

How to deploy your Nuxt.js application on Gitlab for continuous integration?

First create a Gitlab CI YAML file and name it **.gitlab-ci.yml** with the following commands:
```yaml
image: node

before_script:
  - npm install

cache:
  paths:
    - node_modules/

pages:
  script:
    - npm run generate
  artifacts:
    paths:
      - public
  only:
    - master
```
Then in **nuxt.config.js**, add these configuration lines:
```javascript
/**
* Gitlab
*/
router: {
   base: '/whatEverName/',  
},
generate: {
   dir: 'public',
},
```
After running the CI job, and on the project’s repository, go to **Settings** then **Pages** and click on `https://namespace.gitlab.io/whatEverName` to navigate your application.
