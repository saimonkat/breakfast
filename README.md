# Fullstack template

This WordPress starting fullstack template is the result of a joint development by [OnePix](https://onepix.net) and [Thousand Lines](https://thousand-lines.com/) teams.

## Table of Contents
- [Template features](#template-features)
- [Quick start](#quick-start)
- [Webpack](#webpack)
- [Wordpress](#wordpress)
- [Supplements](#supplements)
    - [Notes](#notes)
    - [Validation issues](#validation-issues)


## Template features

1. WordPress theme connected to the Webpack builder in the [assets](assets) folder
2. OOP method for WordPress theme app and classes
3. Browsersync for styles and scripts
4. No HTML files
5. New default structure for SCSS and JS
6. Custom final .css and .js files with custom parts include
7. Svg sprites
8. Images webp conversion

## Quick start

1. Install and setup Wordpress
2. Create empty WP theme, push there files of this template
3. Run local WP server and activate "Fullstack WP starter" theme
4. Connect Composer in the [app](app) folder via `composer install` command
5. Install modules in the [assets](assets) folder via `yarn` command
6. Copy local WP server url to the [assets/webpack/Settings/config.js](assets/webpack/Settings/config.js) file
7. Run webpack via `yarn build:dev` in the [assets](assets) folder

You're all set and ready to create something great!

Hint: you can use webpack localhost server for fullstack development, including WP admin panel on [localhost:3030/wp-admin/](http://localhost:3030/wp-admin/) for example

## Webpack

<img src="https://user-images.githubusercontent.com/22715126/140086271-af88f7e3-8b91-41ed-8d7b-d348d62f8f61.png" height="80" title="Webpack" alt="Webpack">

### Intro

Webpack builder locates in the [assets](assets) folder, contains source files in the [src](assets/src) folder and built files in the [dist](assets/dist) folder.

Source styles components - .scss, output files - .css  
Source scripts components and output files - .js

Styles and scripts in the dist folder connected to the WP theme, so starting webpack dev on the local WP server will provide fullstack live server.

### Installation

For using Webpack you need:

1. node.js version 16+

    Download from the official website [Node.js](https://nodejs.org/en/)

    To change node version you'll need [nvm](https://github.com/coreybutler/nvm-windows/releases) and following commands:

    ```shell
    nvm list
    nvm install 16.0.0
    nvm use 16.0.0
    ```

2. Project manager `yarn`, installed globally

    ``` npm install -g yarn ```

### Commands

Start these commands in the [assets](assets) folder

Packages install / dev / prod:
```shell
yarn
yarn build:dev
```

## WordPress

<img src="https://user-images.githubusercontent.com/22715126/156458136-e0e6c3bc-7038-47a9-97de-1392495e7f20.png" height="80" title="WordPress" alt="WordPress">

### Connection with Webpack:
- Webpack builder locates in the [assets](assets) folder
- Styles and scripts are connected in the [functions.php](functions.php), in the function enqueue_theme_style_scripts()
- In case it is necessary to pass some variables to the js script you have to use [wp_localize_script()](https://misha.agency/wordpress/wp_localize_script.html). We do not output variable values for scripts in the [header.php](header.php) or [footer.php](footer.php).

### ACF rules:
- Group every section of the frontend to the [ACF group](https://www.advancedcustomfields.com/resources/group/) and create all group fields in the Sub Fields.
- In the section template file, for example [template-parts/front-page/section-example-1.php](template-parts/front-page/section-example-1.php), on the first line of the file we get array with the full group of section fileds and work with subfields as array children.
> Why do this? Because it's easier to get all meta fields for the block by one and only query and then just use array subfields. In addition, such groups are easy to reuse on other pages, ACF allows you to copy the field to other groups of fields.

### Work with hooks and filters:
- In the directory [app](app), in the subfolders, we create classes. Class files are connected automatically, this is implemented in the file [app/Autoloader.php](app/Autoloader.php)  
- Every class should only be responsible for its intended area. You don't create a class with code for everything, everything should be logical.

> Example of use: 
> In the controller [app/Controllers/FrontPageController.php](app/Controllers/FrontPageController.php), in the constructor method we include the check [is_front_page()](https://misha.agency/wordpress/is_front_page.html) and initialize the filter that needed only on the main page.

### Connecting Composer
Start these commands in the [app](app) folder

`composer init`

### Menu programming
To programm menu in 99% cases we use the standard function [wp_nav_menu()](https://misha.agency/wordpress/wp_nav_menu.html). 

If you want to customize the layout of the menu, or add something to it (contacts, for example), we create our custom class and inherit it from the [Walker_Nav_Menu](https://developer.wordpress.org/reference/classes/walker_nav_menu/) and redefine the desired menu rendering methods. For the example, I added to the repository [app/Walkers/HeaderWalker.php](app/Walkers/HeaderWalker.php). As a result, we declare our class in the argument array of the function wp_nav_menu().

```php
wp_nav_menu(
    array(
        'theme_location' => 'header-menu',
        'container'      => '',
        'items_wrap'     => '%3$s',
        'walker'         => new HeaderWalker(),
    )
);
```

## Supplements

### Notes

1. Adblock extension blocks images and sections according to the following criteria:

    - The name of the image contains advertising keywords: 'ad', 'adv', 'banner', etc.
    - The name of the image contains dimensions, e.g. 'img_300x250.jpg'.
    - The image lies in the 'ad', 'adv', 'banner', 'banners' folder
    - The section has a class/id which contains 'ad', 'adv'
 
    Even if you don't have Adblock enabled, it may be enabled for users, so these names of images, classes and paths are best avoided.

### Validation issues

W3C validation can be checked here: https://validator.w3.org/.
There should be no errors or warnings when validating the site.
The most popular issues and their solutions are listed below.

1. Section lacks heading. Consider using h2-h6.

    ![image](https://user-images.githubusercontent.com/22715126/172562566-2fc72070-c8e8-4d58-b781-7705f439655e.png)

    Every section on the site with tag `<section>` should contains heading from `<h2>` to `<h6>`.
    
    According SEO headings should be placed following a hierarchy. That is, first must go h2, then the remaining h3-h6. Therefore, the minimum header for the section will be h2.
    
    If the design / layout does not provide a header, then add a hidden header with the class 'visually-hidden'.
    
    Example: `<h2 class="visually-hidden">Блог</h2>`

2. Element source is missing required attribute srcset.

    ![image](https://user-images.githubusercontent.com/22715126/172562973-9d2cad64-84d7-4c4e-82da-946440cf18b0.png)

    The error is due to webp support. You need to duplicate the src attribute on the data-src for images with the following structure:

    ```html
    <picture class="">
        <source srcset="<?= DIST_URI ?>/img/base/hero.webp" 
            type="image/webp">
        <img src="<?= DIST_URI ?>/img/base/hero.png"
            data-src="<?= DIST_URI ?>/img/base/hero.png" 
            alt="">
    </picture>
    ```

3. Element noscript not allowed as child of element picture.

    ![image](https://user-images.githubusercontent.com/22715126/172584634-3a967f0b-fe36-4fa0-b248-e92b8d2cc0e1.png)

    The error is due to `<noscript>` tags generation by webp support plugin.
    You need to add a filter in `functions.php` to disable these tags:

    ```php
    // Lazyload fix
    function nonoscript_lazyload($in) {
        return str_replace( '<noscript><style>.lazyload{display:none;}</style></noscript>', '', $in ) ;
    }
    add_filter( 'autoptimize_filter_imgopt_lazyload_cssoutput', 'nonoscript_lazyload');
    ```