## Installation


1. *We work on stable, supported and up-to-date versions of packages. We recommend you to do the same.*

```bash
$ composer require bitbag/cms-plugin
```

2. Add plugin dependencies to your `config/bundles.php` file:

```php
return [
    ...

    FOS\CKEditorBundle\FOSCKEditorBundle::class => ['all' => true], // WYSIWYG editor
    BitBag\SyliusCmsPlugin\BitBagSyliusCmsPlugin::class  => ['all' => true],
];
```
The first line above (FOSCKEditorBundle) might have been already added during composer require command.

Install WYSIWYG editor ([FOS CKEditor](https://symfony.com/doc/master/bundles/FOSCKEditorBundle/usage/ckeditor.html))

```bash
$ bin/console ckeditor:install
```

Since FOSCKEditorBundle 2.0, to make Twig render the WYSIWYG editor, you must add some configuration under the `twig.form_themes` config key:

```yaml
# Symfony 2/3: app/config/config.yml
# Symfony 4: config/packages/twig.yaml

twig:
    form_themes:
        - '@FOSCKEditor/Form/ckeditor_widget.html.twig'
        - '@BitBagSyliusCmsPlugin/Form/ckeditor_widget.html.twig'
```

3. Import required config in your `config/packages/_sylius.yaml` file:
```yaml
# config/packages/_sylius.yaml

imports:
    ...
    
    - { resource: "@BitBagSyliusCmsPlugin/Resources/config/config.yml" }
```

4. Import routing in your `config/routes.yaml` file:

```yaml

# config/routes.yaml
...

bitbag_sylius_cms_plugin:
    resource: "@BitBagSyliusCmsPlugin/Resources/config/routing.yml"
```
If You have installed https://github.com/stefandoorn/sitemap-plugin according to its installation instructions
import optional sitemap providers:
```yaml
# config/services.yaml
...
imports:
...
    - { resource: "@BitBagSyliusCmsPlugin/Resources/config/services/sitemap_provider.yml" }
```

and plugin dependency to your `config/bundles.php` file:
```php
return [
    ...

    SitemapPlugin\SitemapPlugin::class => ['all' => true], // Sitemap support
];
```

you will probably need to change the extension of the imported file in 

```yaml
# config/packages/sitemap_plugin.yaml

    imports:
        - { resource: "@SitemapPlugin/Resources/config/config.yaml" }
```

5. Finish the installation by updating the database schema and installing assets:

```
$ bin/console doctrine:migrations:diff
$ bin/console doctrine:migrations:migrate
$ bin/console assets:install --symlink
$ bin/console sylius:theme:assets:install --symlink
```

6. Add plugin assets to your project

We recommend you to use Webpack (Encore), for which we have prepared four different instructions on how to add this plugin's assets to your project:

- [Import webpack config](./01.1-webpack-config.md)*
- [Add entry to existing config](./01.2-webpack-entry.md))
- [Import entries in your entry.js files](./01.3-import-entry.md))
- [Your own custom config](./01.4-custom-solution.md))

<small>* Default option for plugin development</small>


However, if you are not using Webpack, here are instructions on how to add optimized and compressed assets directly to your project templates:

- [Non webpack solution](./01.5-non-webpack.md)

7. Passing required "backend" values to "frontend"

In order to make plugin finally work you need to declare "route", in admin _scripts.html.twig you can pass:

```
<script>
    const route = "{{ path('bitbag_sylius_cms_plugin_admin_ajax_media_by_name_phrase')|escape('js') }}";
</script>
```

Any other approach, that will allow cms pages to read this value in js, under "route" key, will work. 

## Testing & running the plugin
```bash
$ composer install
$ cd tests/Application
$ yarn install
$ yarn run gulp
$ bin/console assets:install public -e test
$ bin/console doctrine:schema:create -e test
$ bin/console server:run 127.0.0.1:8080 -d public -e test
$ open http://localhost:8080
$ vendor/bin/behat
$ vendor/bin/phpspec run
```
