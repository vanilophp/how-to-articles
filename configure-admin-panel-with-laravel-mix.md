---
title: Configuring the Admin Panel with Laravel Mix
excerpt: Learn how to configure and build your admin panel using Laravel Mix.
date: 2025-04-16
tags: [customization, admin]
---

If you already have a **Vite** setup in your Laravel project, follow these steps to switch to using **Laravel Mix** for compiling and building your admin panel assets. 
First, remove the `vite.config.js` file and the `vendor` folder under the resources directory if they were created during the Vite setup.

**Important**:   

Update your layout files to replace the Vite directive:

```js
@vite(['resources/css/app.css', 'resources/js/app.js'])
```

with the standard asset links:

```php
<link rel="stylesheet" href="{{ asset('/css/app.css') }}">
```

## Laravel Mix Configuration

1. **Install Mix**  
   
    Run the following command to install Laravel Mix: `npm install laravel-mix --save-dev`


2. **Create a Mix configuration file**  
   
    Create the `webpack.mix.js` file in the root of your project: `touch webpack.mix.js`


3. **Update the `webpack.mix.js` file**  
   
    Configure the assets for your admin panel:

    ```js
    let mix = require('laravel-mix');
    
    mix.js('resources/js/app.js', 'public/js')
        .js('vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.js', 'public/js/appshell.js')
        .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css')
        .postCss('resources/css/app.css', 'public/css', [
            require('tailwindcss'),
        ]);
    ```
   
4. **Update the `postcss.config.js` file**  
   
    Make sure the `postcss.config.js` file is configured to use autoprefixer:

    ```js
    module.exports = {
        plugins: [
            require('autoprefixer')()
        ]
    }
    ``` 

5. **Update the `package.json` file**  

    Change `"type": "module"` to `"type": "commonjs"`, or remove it entirely

    Add a new script to run Laravel Mix:

    ```json
    "scripts": {
        "dev": "mix"
    }
    ```

6. **Remove the `node_modules` Folder and Reinstall Dependencies**  
   
    To ensure everything is set up correctly, delete your `node_modules` folder and run a fresh install:

    ```text
    rm -rf node_modules
    npm i
    ```
   
7. **Run the dev script**  
   
    Once dependencies are installed, compile your assets by running one of the following commands:

    ```text
    npm run dev
    ```
   
    or

   ```text
   npx mix
   ```

8. **Log in with Super-User Credentials**  

   Navigate to your local shop and log in using the super-user credentials you created earlier


9. **Access the Admin Panel**  
   
    Once logged in, visit the admin panel at the following URL: `your-local-shop/admin/product`

For more details, check out the Vanilo.io [documentation](https://vanilo.io/docs/4.x/admin-installation).
