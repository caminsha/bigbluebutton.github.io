---
layout: page
title: "Greenlight"
category: "install"
date: 2016-12-08 16:29:25
redirect_from: "/install/greenlight.html"
---

BigBlueButton is an open source web conferencing system for online learning. The project’s goal is to enable teachers to provide remote students anywhere in the world a high-quality online learning experience.

**Looking to upgrade from Greenlight 1.0? See [these instructions](#upgrading-from-greenlight-10).**

# Overview

![Greenlight Landing](/images/greenlight/landing.png)
<center>(the Greenlight home/landing page.)</center><br>

Greenlight 2.0 is a ruby on rails application that provides a a simple front-end interface to your BigBlueButton server. GreenLight lets users:

  * Sign up/Login with Twitter, Google, or through the application itself
  * Manage their account settings and user preferences.
  * Create and manage their own personal rooms ([BigBlueButton](https://github.com/bigbluebutton/bigbluebutton) sessions).
  * Invite others to join a session using a simple URL.
  * View recordings and share them with others.

Furthermore, as Greenlight is a ruby on rails application, it is completely configurable and brandable.

# Want to try out our demo version?

We host a public, absolutely free, demo version of Greenlight and BigBlueButton over at [demo.bigbluebutton.org](https://demo.bigbluebutton.org/gl). Here you can create an account and experiment with Greenlight before installing it on your own BigBlueButton server.  **Note:** Recordings will only last for 14 days on the public server.

# Features

Greenlight is a feature rich application that aims to address all your BigBlueButton users needs.  We are constantly expanding Greenlight, and if you have any suggestions, you can open one on the [official Greenlight repo](https://github.com/bigbluebutton/greenlight).

As BigBlueButton and Greenlight are open-source projects, we encourage other developers to contribute. If you want to implement a new feature and submit a pull request, you are more than welcome to do so! For information on contributing to BigBlueButton projects, see [Contributing to BigBlueButton](http://docs.bigbluebutton.org/support/faq.html#contributing-to-bigbluebutton).

## Accounts and Settings

### Sign up / Login

Greenlight has full support for managing user accounts. It currently supports three types of user authentication:

* In-application (Greenlight)
* Google OAuth2
* Twitter OAuth2

![Greenlight Login](/images/greenlight/login.png)

All of these authentication providers are configurable and can be turned on/off individually. Turning off In-application authentication will disable user sign up. This allows you to preconfigure accounts for specific users who you want to have access to your server.

Once you are logged in, you'll see your account appear in the top right corner of the screen. Clicking on this displays a drop-down that allows you to traverse Greenlight.

![Greenlight Nav](/images/greenlight/nav.png)

### Settings

Greenlight also allows users to update their account information at any time, including changing their password, profile image, and updating their custom design settings for Greenlight.

![Greenlight Settings](/images/greenlight/settings.png)

## Rooms

### Using Your Room

Greenlight is built around the concept of rooms. A room, to the user, is a BigBlueButton session that they "own". A user can add custom room settings, start/stop their room, invite others to their room using a short easily communicable URL, track sessions, and more.

![Greenlight Room](/images/greenlight/room.png)

To invite someone to join your room, all you have to do is give them the invite URL on the room page. Once they follow the URL, they'll be presented with an invitation to join your room. If the user doesn't have a Greenlight account, they'll be prompted to enter a name to join the room. Otherwise, Greenlight will use their account name.

![Greenlight Invitation](/images/greenlight/invitation.png)

If the room is running, they'll be instantly join in. However, if the room is not running they'll be added to the wait list. Once the room starts, they'll be automatically joined into the room with the owner. A user can leave the wait list simply by leaving the page.

![Greenlight Waiting](/images/greenlight/waiting.png)

### Creating New Rooms

When you sign up for Greenlight, the application creates your home room which is named "`FIRST_NAME`'s Room". You are free to create as many new rooms as you would like for different purposes. To create a new room, you simply click the "Create Room" button beside your profile in the navigation bar. You will have the option to automatically start the room when you create it.

![Greenlight Create Room](/images/greenlight/create_room.png)

Your new room will then show under your current room, and you can click to switch between them. Your home room is the one with the home icon. You can delete a room using the room drop-down.

![Greenlight Multiple Rooms](/images/greenlight/multiple_rooms.png)

## Recordings

### Viewing Recordings

On your room page, all recordings for that room will be listed at the bottom under the recordings subtitle. This table contains information about the recording, as well as its recorded formats. You can click on any of these formats and you'll open the recording in a new tab.

![Greenlight Recordings](/images/greenlight/recordings.png)

Each recording has a visibility associated with it, which can be changed by clicking on it in the recordings table. By default, it is set to unlisted.

**Public**: everyone can view it if they have the room link.<br>
**Unlisted**: only users who have the recording link can view it.

### Managing Recordings

Using the drop-down in the recordings table, you have the ability to delete a recording or mail a recording to a friend. Keep in mind, emailing an unlisted recording **will** allow the friend access, so if you want a recording to be completely private, don't share the recording link.

Deleted recordings are **not** recoverable, so be sure when deleting a recording.

# Installing on a BigBlueButton Server

To make Greenlight as easy to install as possible, we've created a Docker image that wraps the application. It is **highly** recommended that you use Docker when install Greenlight on a BigBlueButton server. You can install Greenlight without Docker (see [GreenLight without Docker](#greenlight-without-docker)).

You should run all commands in this section as `root` on your BigBlueButton server.

## BigBlueButton Server Requirements

Before you install Greenlight, you must have a BigBlueButton server to install it on. This server must:

* have a version of BigBlueButton 2.0 (or greater).
* have a fully qualified hostname.
* have a valid SSL certificate (HTTPS).

## 1. Install Docker

The official Docker documentation is the best resource for Docker install steps. To install Docker (we recommend installing Docker CE unless you have a subscription to Docker EE), see [Install Docker on Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

Before moving onto the next step, verify that Docker is installed by running:

```
docker -v
```

## 2. Install Greenlight

First, create the Greenlight directory for its configuration to live in.

```
mkdir ~/greenlight && cd ~/greenlight
```

Greenlight will read its environment configuration from the `env` file. To generate this file and install the Greenlight Docker image, run:

```
docker run --rm bigbluebutton/greenlight:v2 cat ./sample.env > env
```

## 3. Configure Greenlight

If you open the `env` file you'll see that it contains information for all of the Greenlight configuration options. Some of these are mandatory.

When you installed in step two, the `env` file was generated at `~/greenlight/env`.

### Generating a Secret Key

Greenlight needs a secret key in order to run in production. To generate this, run:

```
docker run --rm bigbluebutton/greenlight:v2 bundle exec rake secret
```

Inside your `env` file, set the `SECRET_KEY_BASE` option to this key. You don't need to surround it in quotations.

### Setting BigBlueButton Credentials

By default, your Greenlight instance will automatically connect to [test-install.blindsidenetworks.com](https://test-install.blindsidenetworks.com) if no BigBlueButton credentials are specified. To set Greenlight to connect to your BigBlueButton server (the one it's installed on), you need to give Greenlight the endpoint and the secret. To get the credentials, run:

```
bbb-conf --secret
```

In your `env` file, set the `BIGBLUEBUTTON_ENDPOINT` to the URL, and set `BIGBLUEBUTTON_SECRET` to the secret.

### Verifying Configuration

Once you have finished setting the environment variables above in your `env` file, to verify that you configuration is valid, run:

```
docker run --rm --env-file env bigbluebutton/greenlight:v2 bundle exec rake conf:check
```

All three of the tests must pass before you proceed.

## 4. Configure Nginx to Route To Greenlight

Greenlight will be configured to deploy at the `/b` subdirectory. This is necessary so it doesn't conflict with the other BigBlueButton components. The Nginx configuration for this subdirectory is stored in the Greenlight image. To add this configuration file to your BigBlueButton server, run:

```
docker run --rm bigbluebutton/greenlight:v2 cat ./greenlight.nginx | sudo tee /etc/bigbluebutton/nginx/greenlight.nginx
```

Verify that the Nginx configuration file (`/etc/bigbluebutton/nginx/greenlight.nginx`) is in place. If it is, restart Nginx so it picks up the new configuration.

```
systemctl restart nginx
```

This will routes all requests to `https://<hostname>/b` to the Greenlight application. If you wish to use a different relative root, you can follow the steps outlined [here](#using-a-different-relative-root).

Optionally, if you wish to have the default landing page at the root of your BigBlueButton server redirect to Greenlight, add the following entry to the bottom of `/etc/nginx/sites-available/bigbluebutton` just before the last `}` character.

```
location = / {
  return 307 /b;
}
```

To have this change take effect, you must once again restart Nginx.

## 5. Start Greenlight 2.0

There are two ways to start the Greenlight docker container.
* using the `docker run` command.
* running the prebuilt `docker-compose` file.

We suggest using `docker-compose` because it is easy to manage and saves you remembering an extremely long command, but if you don't wish to install `docker-compose`, you can use `docker run`.

### Using `docker-compose`

Install `docker-compose` by following the steps for installing on Linux in the [Docker documentation](https://docs.docker.com/compose/install/). You may be required to run all `docker-compose` commands using sudo. If you wish to change this, check out [managing docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user).

Before you continue, verify that you have `docker-compose` installed by running:

```
docker-compose -v
```

Next, you should copy the `docker-compose.yml` file from the Greenlight image in to `~/greenlight` directory. To do this, run:

```
docker run --rm bigbluebutton/greenlight:v2 cat ./docker-compose.yml > docker-compose.yml
```

Once you have this file, from the `~/greenlight` directory, start the application using:

```
docker-compose up -d
```

This will start Greenlight, and you should be able to access it at `https://<hostname>/b`.

The database is saved to the BigBlueButton server so data persists when you restart. This can be found at `~/greenlight/db`.

All of the logs from the application are also saved to the BigBlueButton server, which can be found at `~/greenlight/logs`.

If you don't wish for either of these to persist, simply remove the volumes from the `docker-compose.yml` file.

To stop the application, run:

```
docker-compose down
```

### Using `docker run`

To run Greenlight using `docker run`, from the `~/greenlight` directory, run the following command:

```
docker run --restart unless-stopped -d -p 5000:80 -v $(pwd)/db/production:/usr/src/app/db/production --env-file env --name greenlight-v2 bigbluebutton/greenlight:v2
```

The database is saved to the BigBlueButton server so data persists when you restart. This can be found at `~/greenlight/db`.

If you wish to extract the logs from the docker container and save them to the BigBlueButton server, add `-v $(pwd)/log:/usr/src/app/log` to the `docker run` command.

Then when you want to stop the docker container, run:

```
docker stop greenlight-v2
```

# Configuring Greenlight 2.0

Greenlight is a highly configurable application. The various configuration options can be found below. When making a changes to the `env` file, in order for them to take effect you must restart you Greenlight container. For information on how to do this, see [Applying `env` Changes](#applying-env-changes).

## User Authentication

Greenlight supports three types of user authentication. You can configure any number of these, and Greenlight will dynamically adjust to which ones are configured.

### In Application (Greenlight)

Greenlight has the ability to create accounts on its own. Users can sign up with their name, email and password and use Greenlight's full functionality.

By default, the ability for anyone to create a Greenlight account is enabled. To disable this, set the `ALLOW_GREENLIGHT_ACCOUNTS` option in your `env` file to false. This will **not** delete existing Greenlight accounts, but will prevent new ones from being created.

### Google OAuth2

You can use your own Google account, but since GreenLight will use this account for sending out emails, you may want to create a Google account related to the hostname of your BigBlueButton server.  For example, if your BigBlueButton server is called `example.myserver.com`, you may want to create a Google account called `greenlight_notifications_myserver`.

You need a Google account to create an OAuth 2 `CLIENT_ID` and `SECRET`.  The will enable users of GreenLight to authenticate with their own Google account (not yours).

Login to your Google account, and click the following link

  [https://console.developers.google.com/](https://console.developers.google.com/)

If you want to see the documentation behind OAuth2 at Google, click the link [https://developers.google.com/identity/protocols/OAuth2](https://developers.google.com/identity/protocols/OAuth2).

![greenlight-google-plus-api](/images/gl-google-plus-api.png)

First, enable the "Google+ API".

   1. Click Dashboard
   1. Click "+Enable APIs"
   1. Click "Google+ API"
   1. Click "Enable"
   1. Click "Credentials"

 Next,

  1. Click "Create credentials
  1. Select "OAuth client ID
  1. Select "Web application"
  1. Under "Authorized redirect URIs" enter "http://hostname/b/auth/google/callback" where hostname is your hostname
  1. Click "Create"

A window should open with your OAuth credentials. In this window, copy client ID and client secret to the `env` file so it resembles the following (your credentials will be different).

~~~
GOOGLE_OAUTH2_ID=1093993040802-jjs03khpdl4dfasffq7hj6ansct5.apps.googleusercontent.com
GOOGLE_OAUTH2_SECRET=KLlBNy_b9pvBGasf7d5Wrcq
~~~

The `GOOGLE_OAUTH2_HD` environment variable is optional and can be used to restrict Google authentication to a specific Google Apps hosted domain.

~~~
GOOGLE_OAUTH2_HD=example.com
~~~

### Twitter OAuth2

You need a Twitter account to create an OAuth 2 client ID and client secret. The will enable users of GreenLight to authenticate with their own Twitter account (not yours).

Login to your Twitter account, and click the following link: [https://apps.twitter.com/](https://apps.twitter.com/).

Next,

  1. Click "Create New App"
  1. Under "Callback URL" enter "http://hostname/b/auth/twitter/callback" where hostname is your hostname
  1. Click "Create your Twitter application"
  1. Click "Keys and Access Tokens" tab

You should see a key and secret.  Add the `Consumer Key (API Key)` (not the OwnerID) and `Consumer Secret (API Secret)` to the `env` file (your values will be different).

~~~
TWITTER_ID=SOj8AkIdeqJuP2asfbbGSBk0
TWITTER_SECRET=elxXJZqPVexBFf9ZJsafd4UTSzpr5AVmcH7Si5JzeHQ9th
~~~

### LDAP Auth

Greenlight is able to authenticate users using an external LDAP server. To connect Greenlight to an LDAP server, you will have to provide values for the environment variables under the 'LDAP Login Provider' section in the `env` file. You need to provide all of the values for LDAP authentication to work correctly.

> `LDAP_SERVER` is the server host.

> `LDAP_PORT` is the server port (commonly 389).

> `LDAP_METHOD` is the authentication method, either 'plain' (default), 'ssl' or 'tls'.

> `LDAP_UID` is the name of the attribute that contains the user id. For example, OpenLDAP uses 'uid'.

> `LDAP_BASE` is the location to look up users.

> `LDAP_BIND_DN` is the default account to use for user lookup.

> `LDAP_PASSWORD` is the password for the account to perform user lookup.

Here are some example settings using an [OpenLDAP](http://www.openldap.org/) server.

~~~
LDAP_SERVER=host
LDAP_PORT=389
LDAP_METHOD=plain
LDAP_UID=uid
LDAP_BASE=dc=example,dc=org
LDAP_BIND_DN=cn=admin,dc=example,dc=org
LDAP_PASSWORD=password
~~~

If your server is still running you will need to recreate the container for changes to take effect.

See [Applying env Changes](#applying-env-changes) section to enable your new configuration.

If you are using an ActiveDirectory LDAP server, you must determine the name of your user id parameter to set `LDAP_UID`. It is commonly 'sAMAccountName' or 'UserPrincipalName'.

LDAP authentication takes precedence over all other providers. This means that if you have other providers configured with LDAP, clicking the login button will take you to the LDAP sign in page as opposed to presenting the general login modal.

## Using a Different Relative Root

By default Greenlight is deployed to the `/b` sub directory. If you are running Greenlight on a BigBlueButton server you must deploy Greenlight to a sub directory to avoid conflicts.

If you with to use a relative root other than `/b`, you can do the following:

1. Change the `RELATIVE_ROOT_URL` environment variable.
1. Update the `/etc/bigbluebutton/nginx/greenlight.nginx` file to reflect the new relative root.
1. Restart Nginx and the Greenlight server.

If you are **not** deploying Greenlight on a BigBlueButton server and want the application to run at root, simply set the `RELATIVE_ROOT_URL` to be blank.

## Setting a Custom Branding Image

Greenlight provides you with the ability to set the branding image that you see on the left side of the header. By default this is set to the BigBlueButton logo. You can change this by setting the `BRANDING_IMAGE` option in the `env` file to a public URL for a png or JPEG image.

~~~
BRANDING_IMAGE=https://www.example.com/example.png
~~~

## Adding Terms and Conditions

Greenlight allows you to add terms and conditions to the application. By adding a `terms.md` file to `app/config/` you will enable terms and conditions. This will display a terms and conditions page whenever a user signs up (or logs on without accepting yet). They are required to accept before they can continue to use Greenlight.

The `terms.md` file is a [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) file, so you can style your terms and conditions as you wish.

To add terms and conditions to your docker container, create a `terms.md` file in the `~/greenlight` directory. Then, add the following volume to your Greenlight run command.

`-v $(pwd)/terms.md:/usr/src/app/config/terms.md`

If you are running Greenlight using the `docker-compose.yml` file, add the following volume:

`- ./terms.md:/usr/src/app/config/terms.md`

# Upgrading from Greenlight 1.0

If you have [Greenlight 1.0](/greenlight-v1.html) installed on a BigBlueButton server, you don't have to do a complete new install to install Greenlight 2.0, although you can if you'd like.

Before upgrading, keep in mind that [you cannot move over your data from Greenlight 1.0 to 2.0](#can-i-copy-over-my-old-greenlight-data). If you aren't okay with losing this data, do **not** upgrade.

To upgrade to Greenlight 2.0 from Greenlight 1.0, complete the following steps.

## 1. Setup the 2.0 Environment

Copy the Greenlight 2.0 sample environment into your env file. If you want to save your Greenlight 1.0 settings, make a copy of the `env` file first. This will also pull the Greenlight 2.0 image.
```
cd ~/greenlight
docker run --rm bigbluebutton/greenlight:v2 cat ./sample.env > env
```

Then follow the steps to [configure Greenlight](#3-configure-greenlight).

## 2. Remove the Existing Database

Backup your existing database, which is stored at `~/greenlight/db/production/production.sqlite3`.

Then, remove the database directory (`~/greenlight/db`). When you first start Greenlight 2.0, it will generate a new database.

```
rm -rf db/
```

## 3. Start Greenlight 2.0

Choose to [start Greenlight 2.0](#5-start-greenlight-20) with either `docker-compose` or `docker run`.

# Remaining on Greenlight 1.0

If you have Greenlight 1.0, you may pull the Greenlight 2.0 Docker image when updating. If you do, you'll see a page similar to this:

![Greenlight Migration Error](/images/greenlight/gl_migration_error.png)

To continue to use Greenlight 1.0, all you need to do is to explicitly specify version 1.0 in the run command. You can do this like so:

```
docker run --restart unless-stopped -d -p 5000:80 -v $(pwd)/db/production:/usr/src/app/db/production --env-file env --name greenlight bigbluebutton/greenlight:v1
```

This will force Greenlight to use version 1.0. For any other Docker commands relating to the image, make sure you specify the `v1` tag.

# Administration

## Applying `env` Changes

After you edit the `env` file, you are required to restart Greenlight in order for it to pick up the changes. Ensure you are in the Greenlight directory when restarting Greenlight.

### If you ran Greenlight using `docker-compose`

Bring down Greenlight using:

```
docker-compose down
```

then, bring it back up.

```
docker-compose up -d
```

### If you ran Greenlight using `docker run`

Stop and remove the Greenlight container using:

```
docker stop greenlight-v2
docker rm greenlight-v2
```

bring back up Greenlight using:

```
docker run --restart unless-stopped -d -p 5000:80 -v $(pwd)/db/production:/usr/src/app/db/production --env-file env --name greenlight-v2 bigbluebutton/greenlight:v2
```

## Updating Greenlight

To update Greenlight, all you need to do is pull the latest image from [Dockerhub](https://hub.docker.com/).

```
docker pull bigbluebutton/greenlight:v2
```

Then, [restart Greenlight](#applying-env-changes).

# Looking for the old Greenlight 1.0 docs?

The old version of Greenlight has been renamed to Greenlight Legacy. It is still available on GitHub under the [v1 branch](https://github.com/bigbluebutton/greenlight/tree/v1), although we highly suggest using the latest version of Greenlight.

You can find the old documentation for Greenlight 1.0 [here](/greenlight-v1.html).

# Can I copy over my old Greenlight data?

Greenlight Legacy uses a much different database schema than that of the current version, so for this reason, it is **not** possible to copy over the data directly.

However, Greenlight does allow administrators to seed accounts. In theory, you could seed new accounts based off the data in your existing Greenlight database, but some data may be lost.

# GreenLight without Docker

You can run GreenLight outside of Docker, which also makes it easy to customize GreenLight (such as changing the landing page).

## Running GreenLight as a Rails application

To run Greenlight without Docker requires having server with ruby on rails installed, checking out the source code, and running GreenLight at the command line as a rails application.  We recommend using a [GitHub](https://github.com/) account to checkout the source for GreenLight.

1. [Install Ruby on Rails](https://gorails.com/setup/ubuntu/16.04) on your serer.
1. Login to your GitHub account (via the web) and fork the [GreenLight repository](https://github.com/bigbluebutton/greenlight).
1. Login to your server (via SSH) clone the forked repository with the following command (replace `<GitHub_Username>` with your GitHub username):

   ~~~
   git clone https://github.com/<GitHub_Username>/greenlight.git
   ~~~

1. Next, enter the `greenlight` directory and copy the `sample.env` file to `.env` (this creates a default configuration file):

   ~~~
   cd greenlight
   cp sample.env .env
   ~~~
  
If you want to modify the default configuration file, such as enabling authentication using OAuth2, see [Configuring Greenlight 2.0](#configuring-greenlight-20).  At a minimum you'll need to [Setup the BigBlueButton credentials](#setting-bigbluebutton-credentials).

With rails installed, GreenLight source checked out, and an `.env.` file created, you can now run Greenlight locally like any other rails application. To run Greenlight, use the following command:

~~~
bin/rails server --port=3000
~~~

You can test the application by loading the following URL in your browser: [http://localhost:3000](http://localhost:3000).


## Customizing the Landing Page

A common customization is to modify the default landing page. For a simple change, let's rename the welcome banner to say “Welcome to MyServer”.

The welcome banner is generated by [index.html.erb](https://github.com/bigbluebutton/greenlight/blob/master/app/views/main/index.html.erb).  To customize this message, open `app/views/main/index.html.erb` in an editor.

~~~erb
<%
# BigBlueButton open source conferencing system - http://www.bigbluebutton.org/.
# Copyright (c) 2018 BigBlueButton Inc. and by respective authors (see below).
# This program is free software; you can redistribute it and/or modify it under the
# terms of the GNU Lesser General Public License as published by the Free Software
# Foundation; either version 3.0 of the License, or (at your option) any later
# version.
#
# BigBlueButton is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
# PARTICULAR PURPOSE. See the GNU Lesser General Public License for more details.
# You should have received a copy of the GNU Lesser General Public License along
# with BigBlueButton; if not, see <http://www.gnu.org/licenses/>.
%>

<% unless flash.empty? %>
  <%= render "shared/error_banner" do %>
    <% flash.each do |key, value| %>
      <%= content_tag :div, value, class: "flash #{key} d-inline" %>
    <% end %>
  <% end %>
<% end %>

<div class="background">
  <div class="container pt-9 pb-8">
    <div class="row">
      <div class="col-md-12 col-sm-12 text-center">
        <h1 id="main-text" class="display-4 mb-4"> <%= t("landing.welcome").html_safe %></h1>
        <p class="lead offset-lg-2 col-lg-8 col-sm-12 "><%= t("landing.about", href: link_to(t("greenlight"), "https://bigbluebutton.org/2018/07/09/greenlight-2-0/", target: "_blank")).html_safe %></p>
        <%= link_to "https://youtu.be/Hso8yLzkqj8", target: "_blank" do %>
          <h4><%= t("landing.video") %> <i class="far fa-play-circle ml-1"></i></h4>
        <% end %>
      </div>

    </div>
  </div>
</div>

<%= render "shared/features" %>


<script>
var cycleImages = function(){
  var images = $('.img-cycle img');
  var now = images.filter(':visible');
  var next = now.next().length ? now.next() : images.first();
  var speed = 1500;
  now.fadeOut(speed);
  next.fadeIn(speed);
}
$(function() {
  setInterval(cycleImages, 5000);
});
</script>
~~~

This is an Embedded RuBy (ERB) file.   Look for the following line:

~~~html
<h1 id="main-text" class="display-4 mb-4"> <%= t("landing.welcome").html_safe %></h1>
~~~

The function `t("landing.welcome")` retrieves the localized version of the label `welcome.message`.  For English, this retrieves the string from [en.yml](https://github.com/bigbluebutton/greenlight/blob/master/config/locales/en.yml).  Edit `config/locales/en.yml` and look for the following section:

~~~yml
  landing:
    about: "%{href} is a simple front-end for your BigBlueButton open-source web conferencing server. You can create your own rooms to host sessions, or join others using a short and convenient link."
    welcome: Welcome to BigBlueButton.
    video: Watch our tutorial on using Greenlight
    upgrade: Show me how to upgrade to 2.0!
    version: We've released a new version of Greenlight, but your database isn't compatible.
~~~

To change the welcome message, modify the text associated with `landing.welcome` to say "Welcome to MyServer".

~~~yml
    welcome: Welcome to MyServer
~~~

Save the change to `en.yml`.  Refresh the landing page in your browser.  The welcome message should have the new text.

![Updated login](/images/greenlight/gl-welcome-to-my-server.png)


## Enabling Omniauth

To enable Omniauth, you will require a hostname that ends with a **top level domain** (e.g “.com”, “.ca”, etc).

To do this you can add a hostname to your computer which ends in a **top level domain**:

- [Adding hosts for linux](https://www.cyberciti.biz/faq/ubuntu-change-hostname-command/)
- [Adding hosts for Windows](https://support.rackspace.com/how-to/modify-your-hosts-file/)
- [Adding hosts for Mac](https://www.tekrevue.com/tip/edit-hosts-file-mac-os-x/)

After properly adding the new hostname, run the following:

    bin/rails server --binding=<hostname>

You should see that the URL contains the hostname with the top level domain:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_860B5671A1EBC17AA9B4E38FD1C99F6FBD35D15FD13FAFC83B3C452349A53D30_1538074544244_image.png)


Once everything works, you can [configure Omniauth](http://docs.bigbluebutton.org/install/greenlight-v2.html#user-authentication).

After configuring Omniauth, you should be able to gain full access to Omniauth signup:

![](https://d2mxuefqeaa7sj.cloudfront.net/s_860B5671A1EBC17AA9B4E38FD1C99F6FBD35D15FD13FAFC83B3C452349A53D30_1538074632840_image.png)