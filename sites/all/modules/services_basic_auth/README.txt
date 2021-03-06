Services Basic Authentication
=============================

This module adds HTTP basic authentication to the Services module.

*WARNING* HTTP basic authentication username and password are sent as
plain-text and so represent a security weakness. We strongly recommend
using secure (https) traffic for all requests which make use of this
authentication mechanism.

Reference links:

http://tools.ietf.org/html/rfc2617
https://en.wikipedia.org/wiki/Basic_access_authentication


Installation
------------

Unpack the module and place the services_basic_auth folder in your site's
module directory (e.g. sites/all/modules).


Configuration
-------------

There is no module configuration required, however you will need to enable the
basic authentication provider for each of the services which require basic
authentication, as follows:
 * 1. Enable this module.
 * 2. Visit the services configuration page at:
   http://example.com/admin/structure/services
 * 3. Select the Edit operation for the services endpoint you would like to add
   basic authentication to.
 * 4. Tick the 'HTTP basic authentication' option in the Authentication
   section.
 * 5. Save the endpoint configuration.


Basic usage
-----------

This module checks the HTTP Authorization header and authenticates the request
based on the content.

If an 'Authorization' header is included with the request to a services
endpoint then this module will attempt to authenticate the user with the
credentials provided.

The authentication credentials are generated by creating a 'username:password'
string and applying base64 encoding.

 * Example using curl command-line tool:

   curl -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" https://example.com/endpoint/node.json

 * Example using PHP curl library:

   <?php
   // Endpoint URL.
   $url = 'https://example.com/endpoint/node.json';
   $ch = curl_init();
   curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
   curl_setopt($ch, CURLOPT_URL, $url);
   // Set authentication details.
   curl_setopt($ch, CURLOPT_USERPWD, "username:password");
   // Fetch the results.
   $result = curl_exec($ch);
   curl_close($ch);
   ?>

 * Example using Guzzle:

   <?php
   $client = new GuzzleHttp\Client(['base_url' => 'https://example.com']);
   $response = $client->get('/endpoint/node.json', ['auth' => ['username', 'password']]);
   ?>


See also:
http://drupal.stackexchange.com/questions/109675/how-to-use-drupal-7-services-basic-authentication


CGI/FastCGI compatibility
-------------------------

If you are using the CGI/FastCGI server API, you must apply a patch to your
.htaccess file for basic authentication to work.

You can either apply the included patch "htaccess-fastcgi.patch" or add the
following rewrite rule to your .htaccess file manually:

    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

By default the HTTP_AUTHORIZATION header is used by this module. In some cases
Apache will use a different header e.g. REDIRECT_HTTP_AUTHORIZATION. You can
configure which header should be used in settings.php:

  $conf['services_basic_auth_fastcgi_header'] = 'REDIRECT_HTTP_AUTHORIZATION';
