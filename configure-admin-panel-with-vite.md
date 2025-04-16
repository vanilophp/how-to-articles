---
title: Configuring the Admin Panel with Vite
excerpt: Learn how to configure and build your admin panel using Vite with Laravel.
date: 2025-04-16
tags: [customization, admin]
---

If you already have a Laravel project using Vite, and you’d like to use Vite for compiling and building your admin panel assets as well, this guide will walk you through the necessary configuration steps.

> Note that when using Vite, the konekt/appshell package must be updated to version 4.12 or higher via Composer.

## Vite config

### 1. Update your `vite.config.js`

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import path from 'path';
import fs from 'fs';
import { join } from 'path';

function createCssDirPlugin() {
    return {
        name: 'create-css-dir',
        buildEnd() {
            const cssDir = join('public', 'css');
            if (!fs.existsSync(cssDir)) {
                fs.mkdirSync(cssDir, { recursive: true });
            }
        }
    };
}

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/js/app.js',
                'vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.esm.js',
                'resources/css/app.css',
                'vendor/konekt/appshell/src/resources/assets/sass/appshell.sass',
            ],
            refresh: true,
        }),
        createCssDirPlugin()
    ],
    resolve: {
        alias: {
            '~bootstrap': path.resolve(__dirname, 'node_modules/bootstrap'),
        },
    },
    build: {
        outDir: 'public',
        rollupOptions: {
            output: {
                assetFileNames: (assetInfo) => {
                    if (assetInfo.name === 'appshell.css') {
                        return 'css/appshell.css';
                    }

                    return 'assets/[name]-[hash][extname]';
                },
            },
        },
        emptyOutDir: false,
    },
});
```

### 2. Create a `_js.blade.php` file  
    
Next, create a `_js.blade.php` file inside the `resources/views/vendor/appshell/layouts/default/` directory and paste the following code into it:

```php
@vite(['vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.esm.js'])

<style>
    [x-cloak] { display: none !important; }
</style>
```
   
### 3. Build your assets  
    
Build your assets by running the `npm run build` command in your terminal

### 4. Start the Vite development server  
    
After building the assets, run `npm run dev` command to start the Vite development server

### 5. Log in with super-user credentials  
    
Navigate to your local shop and log in using the super-user credentials you created earlier

### 6. Access the admin panel  
    
To view the admin panel, visit the following URL: `your-local-shop/admin/product`

For more details, check out the Vanilo.io [documentation](https://vanilo.io/docs/4.x/admin-installation).
