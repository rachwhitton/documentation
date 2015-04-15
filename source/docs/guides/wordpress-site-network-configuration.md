#Prerequisites#
Pantheon does not currently support wildcard domains

Don't actually create sites in dev/test and expect them to work across environments
#Create WordPress site#
#Change to a paid service Level#
#register domain#
#Add DNS entries#

#Edit wp-config.php#
```
define( 'WP_ALLOW_MULTISITE', true );
```

dashboard>admin> tools>network setup

Choose subdomains

next

fail this test

>Warning! Wildcard DNS may not be configured correctly!
>The installer attempted to contact a random hostname (37f188.dev-sitenetwork1.pantheon.io) on your domain. This resulted in an error message:Could not resolve host: 37f188.dev-sitenetwork1.pantheon.io

>To use a subdomain configuration, you must have a wildcard entry in your DNS. This usually means adding a * hostname record pointing at your web server in your DNS configuration tool.
>You can still use your site but any subdomain you create may not be accessible. If you know your DNS is correct, ignore this message


add to wp-config.php
```
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true);
if (isset($_ENV['PANTHEON_ENVIRONMENT'])){
  if ($_ENV['PANTHEON_ENVIRONMENT']=='dev'){
    define('DOMAIN_CURRENT_SITE', 'dev-sitenetwork1.pantheon.io');
  } elseif($_ENV['PANTHEON_ENVIRONMENT']=='test') {
    define('DOMAIN_CURRENT_SITE', 'test.pantheon.me');
  } else {
    define('DOMAIN_CURRENT_SITE', 'pantheon.me');
  }
define('PATH_CURRENT_SITE', '/');
define('SITE_ID_CURRENT_SITE', 1);
define('BLOG_ID_CURRENT_SITE', 1);
```
#Adding network sites#

#defurbling the database#
Run these once
```
UPDATE wp_site SET domain = replace(domain, 'dev.mydomain.com', 'test.mydomain.com');

UPDATE wp_sitemeta SET meta_value = replace(meta_value, 'dev.mydomain.com', 'test.mydomain.com') WHERE meta_key = 'siteurl';

UPDATE wp_blogs SET domain = replace(domain, 'dev.mydomain.com','test.mydomain.com');
```

Run these for each site (wp_2_options, wp_3_options, etc.)
```
UPDATE wp_options SET option_value = replace(option_value, 'dev.mydomain.com', 'test.mydomain.com') WHERE option_name = 'home' OR option_name = 'siteurl';

UPDATE wp_posts SET guid = replace(guid, 'dev.mydomain.com','test.mydomain.com');

UPDATE wp_posts SET post_content = replace(post_content, 'dev.mydomain.com', 'test.mydomain.com');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'dev.mydomain.com','test.mydomain.com');

```
##configuration##
wp_site.domain
wp_options option_name=siteurl, option_name=home
wp_*_options option_name=siteurl, option_name=home
wp_blogs.domain
##content##
wp_*_posts.guid
wp_posts.guid
##unknown##
wp_signups?
