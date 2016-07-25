---
layout: default
title: Using AngularJS
permalink: /using-angularjs/
redirect_from:
  - /using_angularjs.html
sitemap:
    priority: 0.7
    lastmod: 2015-01-29T23:41:00-00:00
---

# <i class="fa fa-html5"></i> Using AngularJS

## Project Structure

The JHipster client code can be found under `src/main/webapp`, and follows closely the  [John Papa AngularJS 1 style guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md). Please read this guide first if you have any question on our application structure, file names, JavaScript conventions...

This style guide is endorsed by the AngularJS team, and gives the guarantee to have a clear upgrade path to AngularJS 2.

For AngularJS routes we follow a dash cased naming convention so that the URLs are clean and consistent.
When you generate an entity the route names, route URLs and REST API endpoint URLs are generated according to this convention, also entity names are automatically pluralized where required.

Here is the main project structure:

    webapp
    ├── app                               - Your application
    │   ├── account                       - User account management UI
    │   ├── admin                         - Administration UI
    │   ├── blocks                        - Common building blocks like configuration and interceptors
    │   ├── components                    - Common components like alerting and form validation
    │   ├── entities                      - Generated entities (more information below)
    │   ├── home                          - Home page
    │   ├── layouts                       - Common page layouts like navigation bar and error pages
    │   ├── services                      - Common services like authentication and user management
    │   ├── app.constants.js              - Application constants
    │   ├── app.module.js                 - Application modules configuration
    │   ├── app.state.js                  - Main application router
    ├── bower_components                  - Dependencies installed by Bower
    ├── content                           - Static content
    │   ├── images                        - Images
    │   ├── styles                        - CSS stylesheets
    │   ├── fonts                         - Font files will be copied here
    ├── i18n                              - Translation files
    ├── scss                              - Sass style sheet files will be here if you choose the option
    ├── swagger-ui                        - Swagger UI front-end
    ├── 404.html                          - 404 page
    ├── favicon.ico                       - Fav icon
    ├── index.html                        - Index page
    ├── robots.txt                        - Configuration for bots and Web crawlers

Using the [entity sub-generator]({{ site.url }}/creating-an-entity/) to create a new entity called `Foo` generates the following front-end files under `src/main/webapp`:

    webapp
    ├── app
    │   ├── entities
    │       ├── foo                                    - CRUD front-end for the Foo entity
    │           ├── foo.controller.js                  - Controller for the list page
    │           ├── foo.service.js                     - Service which access the Foo REST resource
    │           ├── foo.state.js                       - AngularUI router, which manages routes for this entity
    │           ├── foo-delete-dialog.controller.js    - Controller for the delete dialog pop-up
    │           ├── foo-delete-dialog.html             - View for the delete dialog pop-up
    │           ├── foo-detail.controller.js           - Controller for the entity details page
    │           ├── foo-detail.html                    - View for the entity details page
    │           ├── foo-dialog.controller.js           - Controller for the create/update dialog pop-up
    │           ├── foo-dialog.html                    - View for the create/update dialog pop-up
    │           ├── foos.html                          - View for the list page
    ├── i18n                                           - Translation files
    │   ├── en                                         - English translations
    │   │   ├── foo.json                               - English translation of Foo name, fields, ...
    │   ├── fr                                         - French translations
    │   │   ├── foo.json                               - French translation of Foo name, fields, ...

Please note that the default language translations would be based on what you have choosen during app generation. 'en' and 'fr' are shown here only for demonstration.

## Authorizations

JHipster uses [angular-ui-router](http://angular-ui.github.io/ui-router/) to organize the different parts of your client application.

对于每一个路由节点,必须的权限在路由的date属性里配置,当权限列表为空的时候意味着这块路由可以被任何匿名者访问.

权限名称在服务端的类文件`AuthoritiesConstants.java`定义.

在下面的例子里,`.state('sessions',{})`只能被具有`ROLE_USER`权限的用户访问(对`ROLE_USER`的用户认证).

    (function() {
        'use strict';

        angular
            .module('tmpApp')
            .config(stateConfig);

        stateConfig.$inject = ['$stateProvider'];

        function stateConfig($stateProvider) {
            $stateProvider.state('sessions', {
                parent: 'account',
                url: '/sessions',
                data: {
                    authorities: ['ROLE_USER'],
                    pageTitle: 'global.menu.account.sessions'
                },
                views: {
                    'content@': {
                        templateUrl: 'app/account/sessions/sessions.html',
                        controller: 'SessionsController',
                        controllerAs: 'vm'
                    }
                },
                resolve: {
                    translatePartialLoader: ['$translate', '$translatePartialLoader', function ($translate, $translatePartialLoader) {
                        $translatePartialLoader.addPart('sessions');
                        return $translate.refresh();
                    }]
                }
            });
        }
    })();

## 提示系统

JHipster使用[ui-bootstrap alerts](https://angular-ui.github.io/bootstrap/#/alert)作为提示系统,生成在具有国际化能力的模块`AlertService`里,在项目中使用.

Jhipster默认显示成功提示当一个实体创建/更新/删除,当有错误提示时,将从后端捕获一个错误响应.

自定义通知或提醒，使用下面的方法将`AlertService`注入你的控制器器，指令或服务。

`success`, `info`, `warning` and `error`这些方法提示将会有5秒的延迟显示,其他的配置使用`add`方法:

    (function() {
        'use strict';

        angular
            .module('tmpApp')
            .controller('FooController', FooController);

        FooController.$inject = ['$scope', '$state', 'Foo', 'AlertService'];

        function FooController ($scope, $state, Foo, AlertService) {

            AlertService.success("This is a success message, it is green");

            AlertService.info("This is an info message, it is blue");

            AlertService.warning("This is a warning message, it is amber");

            AlertService.error("This is an error message, it is red");

            AlertService.success("i.will.be.translated");
            // where key i.will.be.translated needs to be in global.json

            AlertService.success("i.will.be.translated", {param: someParam});
            // where key i.will.be.translated needs to be in global.json and can have a { param } which will be replaced by `someParam`

            AlertService.add({
                type: "success", // can be success, info, warning and error
                msg: msg,
                params: params, // parameters to pass for translation
                timeout: timeout // how long to show the alert in milliseconds
            });

            AlertService.clear();  // clear all alerts

            AlertService.get(); // get all open alerts

        }
    })();

如果你想提示让提示的方式像Toasts(Bootstrap Toastr JS显示效果),你可以这样设置将`src/main/webapp/app/blocks/config/alert.config.js`下的设置为`AlertServiceProvider.showAsToast(true)`,默认情况下为`false`,`AlertServiceProvider.showAsToast(false)`.
