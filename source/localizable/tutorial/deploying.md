To deploy an Ember application simply transfer the output from `ember build` to a web server.
This can be done with standard Unix file transfer tools such as `rsync` or `scp`.
There are also services that will let you deploy easily.

## Deploying with scp

You can deploy your application to any web server by copying the output from `ember build` to any web server:

```shell
ember build
scp -r dist/* myserver.com:/var/www/public/
```

## Deploying to surge.sh

[Surge.sh](http://surge.sh/) allows you to publish any folder to the web for free.
To deploy an Ember application you can simply deploy the folder produced by `ember build`.

You will need to have the surge cli tool installed:

```shell
npm install -g surge
```

Then you can use the `surge` command to deploy your application.
Note you will also need to provide a copy of index.html with the filename 200.html
so that surge can support Ember's client-side routing.

```shell
ember build --environment=development
cd dist
cp index.html 200.html
surge
```

Press return to accept the defaults when deploying the first time.
You will be provided with a URL in the form `funny-name.surge.sh` that you can use for repeated deployments.

So to deploy to the same URL after making changes, perform the same steps, this time providing the URL for your site:

```shell
rm -rf dist
ember build --environment=development
cd dist
cp index.html 200.html
surge funny-name.surge.sh
```

We use `--enviroment=development` here so that Mirage will continue to mock fake data.
However, normally we would use `ember build --environment=production` which does more to make your code ready for production.

## Servers

### Apache

On an Apache server, the rewrite engine (mod-rewrite) must be enabled in order for Ember routing to work properly.
If you upload your dist folder, going to your main URL works, but when you try to go to a route such as '{main URL}/example' and it returns 404, your server has not been configured for "friendly" URLs.
To fix this, if it doesn't exist, add a file called '.htaccess' (just a period at the beginning, nothing before it) to the root folder of your website.
Add these lines:

```text
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteRule ^index\.html$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) index.html [L]
</IfModule>
```

Your server's configuration may be different so you may need different options. Please see http://httpd.apache.org/docs/2.0/misc/rewriteguide.html for more information.
