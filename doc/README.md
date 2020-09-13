## Introduction
I was working on a project written with [Nuxt.js](https://nuxtjs.org/). As you know Nuxt.js is created on top of the [Vue.js](https://vuejs.org/), so they share the same component based approach. And based on that, we split the whole application into small and reusable components.<br/>

We didn’t use any ready-to-use UI library/framework for our components (such as Quasar, Vuetify) so all of our components were written by our team, because our UI designers were very detailed and we need to have flexibility and customization in any level to create pixel perfect user interfaces based on their designs.
We kept all of our components inside the components directory of our Nuxt.js app as it recommends.

## A demand
After a while we had a demand to create a dashboard (admin panel) for our internal works and other activities which we couldn’t put them to the main website and it was better we kept them in a separate project. So we chose Vue.js again because it’s a stack of our main website (we tried to keep the same stack in our front-end projects as possible) and most importantly we needed lots of components on Vue.js which we already had on our main website and obviously we didn’t want to write them from the  scratch (because it’s ridiculous!) and we definitely didn’t want to keep them in two separate places and make maintaining a disaster in the future. Imagine every single change and bug fix should be taken care of in two places!!! Impossible right! Or should I say stupid!<br/>

Long story short, we must put all of our components in a separated project and use it in our main website and newly created dashboard as a dependency. Which seems a best practice to me so far and many people do this around the globe.<br/>

So this new project should contain our components and these components should be used in other projects. So we have no pages here! There is no point to have some empty pages or some pages to just show a bunch of components and it’s a waste of energy and time (because we can use a storybook (https://storybook.js.org/) and I’m going to talk about it in another article). This led me to find out about vue library projects. These projects have no App.vue (as a starting point for a showable web application) and just contain a bunch of components, helpers, utils and other stuff to be shared and used in a couple of other projects. We can write proper tests for them and keep any related logic and functionality there. The upper layer (the other apps which use our projects) don’t need to know about our internal stuff and with a proper build they can use it like any other dependencies.<br/>

## Solution
Fortunately for us, vue-cli (https://cli.vuejs.org/) made it very easy by a few steps which I’m going to explain here. It uses the Webpack (https://webpack.js.org/) behind the scenes and provides lots of useful tools.
Of Course there are other ways like using Webpack directly which is more flexible but we didn’t need that kind of flexibility and we were out of time.
If you don’t know about Webpack  and how to configure it, I strongly recommend to take a deep look at it first. If you just use things like vue-cli and don’t learn about what they’re doing behind the scenes, it might get you away from basic stuff and deep knowledge which will cause you many problems in the future and it’s a poison for any kind of engineer.<br/>

## How to create and use
Now the steps:<br/>
1. Install vue-cli to use its tools and templates. By below command we’re going to install it globally on our machine:<br/>
```
npm install -g @vue/cli
```
2. Create a new project with it:
```
vue create [your-awesome-project]
```
3. Set your required configurations:
I’m just choosing Babel (https://babeljs.io/) and nothing more to have a very light project.
4. Go to your root directory and open the project with your preferred editor or IDE. I use VsCode (https://code.visualstudio.com/). 
5. Run the project with just typing:
```
npm run serve
```
As you can see it’s a simple template to run a simple vue project.

6. Remove App.vue in the src folder. You don’t need it! Also you can get rid of logo.png in the assets folder and HelloWord.vue in components folder.

7. Create your first component.
I just created a simple component for this example.
```
<template>
  <button class="a-button">
    {{ text }}
  </button>
</template>
 
<script>
export default {
  name: 'Button',
  props: {
    text: {
      type: String,
      default: ''
    }
  }
}
</script>
 
<style scoped>
.a-button {
  font-weight: 500;
  outline: 0;
  border: 0;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
  text-align: center;
}
</style>
```
It’s a button which takes a string as a prop (text) and shows it in it’s Inner Html.

8. Export this component to be used by others. For that:
Add an index.js file into components folder with just below content:
```
import Button from './Button'
export { Button }
```
It will export Button.vue by name to the upper layer.
Then replace the content of the main.js in src folder with these:
```
export * from './components'
```
By that we’re saying to main.js to export everything in components directory as named export. You can use any other way you prefer.
9. Replace your current build script in package.json with the line below:
```
"build": "vue-cli-service build --target lib --name ui-components src/main.js"
```
By using --target we can specify the type of build we want (https://cli.vuejs.org/guide/build-targets.html). Also we can declare a name and an entry point to start the building process and Webpack, in our case main.js.
When you run npm run build, this script is gonna build a folder (by default dist) inside your root directory by webpack and we told vue.js to make it as a library and not a website.

10. By running the build script, as you can see we have dist folder like this:<br>
![dist folder after build](https://github.com/aliafsah1988/vue-lib-sample/blob/doc/aliafsah-dist-screen.png?raw=true)
<br>
Each file can be used in a different situation.
A lib build outputs:<br>

dist/[YOUR_LIBRARY].common.js: A CommonJS bundle for consuming via bundlers.<br>
dist/[YOUR_LIBRARY].umd.js: A UMD bundle for consuming directly in browsers or with AMD loaders<br>
dist/[YOUR_LIBRARY].umd.min.js: Minified version of the UMD build.<br>
dist/[YOUR_LIBRARY].css: Extracted CSS file (can be forced into inlined by setting css: { extract: false } in vue.config.js)
<br>

11. So if we want to use this project as a dependency package, we should use [YOUR_LIBRARY].common.js as the primary entry point to our project. For that we just need to set the main property in package.json:
```
"main": "./dist/vue-lib-sample.common.js"
```
Note: If you’re using git, make sure you removed the dist folder from .gitignore file so you can have it on your remote repo.
12. Now we can use it as a npm package or anyway we want. In this example I’m going to use it directly as a local dependency in another project.
In my upper layer project I’m just installing our project as dependency with npm:
```
npm add [PATH_OF_PROJECT]
```
So my package.json will be updated like this:
```
"dependencies": {
    ...
    "vue-lib-sample": "file:[some_path]/vue-lib-sample"
}
```
And as you can see it’s added to our node_modules:

Note: In this approach (local dependency) you might face some linting issues. In my case I didn’t use any linting in both projects.

Now we can use our Button component in any where we want!
```
<template>
  <div class="hello">
    <Button size="small"
        theme="border" :text="'hello'" />
  </div>
</template>
 
<script>
import { Button } from "vue-lib-sample";
import "vue-lib-sample/dist/vue-lib-sample.css"
 
export default {
  name: 'HelloWorld',
  components:{
    Button
  },
  props: {
    msg: String
  }
}
</script>
```

## Conclusion
It’s a good practice to keep your components in a separated project, and isolate them from outside. They’re going to be reusable and you just need to change them in just one place. Also by adding a storybook you can make life easier for the whole team, including developers, designers and everybody else.