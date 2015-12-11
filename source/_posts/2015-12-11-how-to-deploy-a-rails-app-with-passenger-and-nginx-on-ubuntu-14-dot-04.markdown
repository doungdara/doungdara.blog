---
layout: post
title: "How To Deploy a Rails App with Passenger and Nginx on Ubuntu 14.04"
date: 2015-12-11 15:05:29 +0700
comments: true
categories:
---
<div class="content-body tutorial-content" data-growable-markdown="">
      <div name="introduction" data-unique="introduction"></div><h2 id="introduction">Introduction</h2>

<p>If you are a Ruby on Rails developer, you probably need a web server to host your web apps. This tutorial shows you how to use <a href="https://www.phusionpassenger.com/">Phusion Passenger</a> as your Rails-friendly web server. Passenger is easy to install, configure, and maintain and it can be used with Nginx or Apache. In this tutorial, we will install Passenger with Nginx on Ubuntu 14.04.</p>

<p>An alternate method to deploy your Rails app is with this <a href="https://www.digitalocean.com/community/tutorials/how-to-use-the-1-click-ruby-on-rails-on-ubuntu-14-04-image">1-Click Rails Installation</a> that uses Nginx with <a href="http://unicorn.bogomips.org/">Unicorn</a>, a HTTP server that can handle multiple requests concurrently.</p>

<p>By the end of this tutorial, you will have a test Rails application deployed on your Passenger/Nginx web server and accessible via a domain or IP address.</p>

<div name="step-one-—-create-your-droplet" data-unique="step-one-—-create-your-droplet"></div><h2 id="step-one-—-create-your-droplet">Step One — Create Your Droplet</h2>

<p>Create a new Ubuntu 14.04 Droplet. For smaller sites, it is enough to take the 512 MB plan.</p>

<p class="growable"><img src="https://assets.digitalocean.com/articles/Rails_Passenger_Nginx/1.png" alt="Droplet size"></p>

<p>You may want to choose the 32-bit Ubuntu image because of smaller memory consumption (64-bit programs use about 50% more memory then their 32-bit counterparts). However, if you need a bigger machine or there is a chance that you will upgrade to more than 4 GB of RAM, you should choose the 64-bit version.</p>

<p class="growable"><img src="https://assets.digitalocean.com/articles/Rails_Passenger_Nginx/2.png" alt="Droplet image"></p>

<div name="step-two-—-add-a-sudo-user" data-unique="step-two-—-add-a-sudo-user"></div><h2 id="step-two-—-add-a-sudo-user">Step Two — Add a Sudo User</h2>

<p>After the Droplet is created, additional system administration work is needed. You should create a system user and secure the server.</p>

<p>Follow the <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04">Initial Server Setup</a> article.</p>

<p>In this tutorial, you should create a basic user with sudo privileges. We will use the <em>rails</em> user in this example. If your user has another name, make sure that you use correct paths in the next steps.</p>

<div name="step-three-(optional)-—-set-up-your-domain" data-unique="step-three-(optional)-—-set-up-your-domain"></div><h2 id="step-three-optional-—-set-up-your-domain">Step Three (Optional) — Set Up Your Domain</h2>

<p>In order to ensure that your site will be up and visible, you need to set up your DNS records to point your domain name towards your new server. You can find more information on <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean">setting up a hostname</a> by following the link.</p>

<p>However, this step is optional, since you can access your site via an IP address.</p>

<div name="step-four-—-install-ruby" data-unique="step-four-—-install-ruby"></div><h2 id="step-four-—-install-ruby">Step Four — Install Ruby</h2>

<p>We will install Ruby manually from source.</p>

<p>Before we do anything else, we should run an update to make sure that all of the packages we want to install are up to date:</p>
<pre class="code-pre "><code langs="">sudo apt-get update
</code></pre>
<p>Next, install some dependencies. This should make the installation as smooth as possible:</p>
<pre class="code-pre "><code langs="">sudo apt-get install build-essential libssl-dev libyaml-dev libreadline-dev openssl curl git-core zlib1g-dev bison libxml2-dev libxslt1-dev libcurl4-openssl-dev nodejs libsqlite3-dev sqlite3
</code></pre>
<p>Create a temporary folder for the Ruby source files:</p>
<pre class="code-pre "><code langs="">mkdir ~/ruby
</code></pre>
<p>Move to the new folder:</p>
<pre class="code-pre "><code langs="">cd ~/ruby
</code></pre>
<p>Download the latest stable Ruby source code. At the time of this writing, this is version 2.1.3. You can get the current latest version from the <a href="https://www.ruby-lang.org/en/downloads/">Download Ruby</a> website. If a newer version is available, you will need to replace the link in the following command:</p>
<pre class="code-pre "><code langs="">wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.3.tar.gz
</code></pre>
<p>Decompress the downloaded file:</p>
<pre class="code-pre "><code langs="">tar -xzf ruby-2.1.3.tar.gz
</code></pre>
<p>Select the extracted directory:</p>
<pre class="code-pre "><code langs="">cd ruby-2.1.3
</code></pre>
<p>Run the <span class="highlight-key-word">configure</span> script. This will take some time as it checks for dependencies and creates a new <strong>Makefile</strong>, which will contain steps that need to be taken to compile the code:</p>
<pre class="code-pre "><code langs="">./configure
</code></pre>
<p>Run the <span class="highlight-key-word">make</span> utility which will use the Makefile to build the executable program. This step can take a bit longer:</p>
<pre class="code-pre "><code langs="">make
</code></pre>
<p>Now, run the same command with the <span class="highlight-key-word">install</span> parameter. It will try to copy the compiled binaries to the <code>/usr/local/bin</code> folder. This step requires root access to write to this directory. It will also take a bit of time:</p>
<pre class="code-pre "><code langs="">sudo make install
</code></pre>
<p>Ruby should now be installed on the system. We can check it with the following command, which should print the Ruby version:</p>
<pre class="code-pre "><code langs="">ruby -v
</code></pre>
<p>Finally, we can delete the temporary folder:</p>
<pre class="code-pre "><code langs="">rm -rf ~/ruby
</code></pre>
<div name="step-five-—-install-passenger-and-nginx" data-unique="step-five-—-install-passenger-and-nginx"></div><h2 id="step-five-—-install-passenger-and-nginx">Step Five — Install Passenger and Nginx</h2>

<p>The preferred method to install Passenger in the past was using a generic installation via RubyGems (<code>passenger-install-nginx-module</code>).</p>

<p>However, you can now install Passenger on Ubuntu with the Advanced Packaging Tool (APT), which is what we'll be using. In this manner, the installation and — even more importantly — the update process for Passenger with Nginx, is really simple.</p>

<p>First, install a PGP key:</p>
<pre class="code-pre "><code langs="">sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 561F9B9CAC40B2F7
</code></pre>
<p>Create an APT source file (you will need sudo privileges):</p>
<pre class="code-pre "><code langs="">sudo nano /etc/apt/sources.list.d/passenger.list
</code></pre>
<p>And insert the following line in the file:</p>
<pre class="code-pre "><code langs="">deb https://oss-binaries.phusionpassenger.com/apt/passenger trusty main
</code></pre>
<p>Press <strong>CTRL+x</strong> to exit, type <strong>y</strong> to save the file, and then press <strong>ENTER</strong> to confirm the file location.</p>

<p>Change the owner and permissions for this file:</p>
<pre class="code-pre "><code langs="">sudo chown root: /etc/apt/sources.list.d/passenger.list
sudo chmod 600 /etc/apt/sources.list.d/passenger.list
</code></pre>
<p>Update the APT cache:</p>
<pre class="code-pre "><code langs="">sudo apt-get update
</code></pre>
<p>Finally, install Passenger with Nginx:</p>
<pre class="code-pre "><code langs="">sudo apt-get install nginx-extras passenger
</code></pre>
<p>This step will overwrite our Ruby version to an older one. To resolve this, simply remove the incorrect Ruby location and create a new symlink to the correct Ruby binary file:</p>
<pre class="code-pre "><code langs="">sudo rm /usr/bin/ruby
sudo ln -s /usr/local/bin/ruby /usr/bin/ruby
</code></pre>
<div name="step-six-—-set-up-the-web-server" data-unique="step-six-—-set-up-the-web-server"></div><h2 id="step-six-—-set-up-the-web-server">Step Six — Set Up The Web Server</h2>

<p>Open the Nginx configuration file:</p>
<pre class="code-pre "><code langs="">sudo nano /etc/nginx/nginx.conf
</code></pre>
<p>Find the following lines, in the <span class="highlight-key-word">http</span> block:</p>
<pre class="code-pre "><code langs=""># passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini;
# passenger_ruby /usr/bin/ruby;
</code></pre>
<p>Uncomment both of them. Update the path in the <span class="highlight-key-word">passenger_ruby</span> line. They should look like this:</p>
<pre class="code-pre "><code langs="">passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini;
passenger_ruby /usr/local/bin/ruby;
</code></pre>
<p>Save and exit the file.</p>

<div name="step-seven-—-deploy" data-unique="step-seven-—-deploy"></div><h2 id="step-seven-—-deploy">Step Seven — Deploy</h2>

<p>At this point you can deploy your own Rails application if you have one ready. If you want to deploy an existing app, you can upload your project to the server and skip to the <code>/etc/nginx/sites-available/default</code> step.</p>

<p>For this tutorial, we will create a new Rails app directly on the Droplet. We will need a the <strong>rails</strong> gem to create the new app.</p>

<p>Move to your user's home directory (otherwise, you will get the error <code>No such file or directory - getcwd</code>) –</p>
<pre class="code-pre "><code langs="">cd ~
</code></pre>
<p>Install the <strong>rails</strong> gem (without extra documentation to install it faster). This will still take a few minutes:</p>
<pre class="code-pre "><code langs="">sudo gem install --no-rdoc --no-ri rails
</code></pre>
<p>Now we can create a new app. In our example, we will use the name <span class="highlight-key-word">testapp</span>. If you want to use another name, make sure you use correct paths. We will skip the Bundler installation because we want to run it manually later.</p>
<pre class="code-pre "><code langs="">rails new testapp --skip-bundle
</code></pre>
<p>Enter the directory:</p>
<pre class="code-pre "><code langs="">cd testapp
</code></pre>
<p>Now we need to install a JavaScript execution environment. It can be installed as the <span class="highlight-key-word">therubyracer</span> gem. To install it, open the <strong>Gemfile</strong>:</p>
<pre class="code-pre "><code langs="">nano Gemfile
</code></pre>
<p>Find the following line:</p>
<pre class="code-pre "><code langs=""># gem 'therubyracer',  platforms: :ruby
</code></pre>
<p>And uncomment it:</p>
<pre class="code-pre "><code langs="">gem 'therubyracer',  platforms: :ruby
</code></pre>
<p>Save the file, and run Bundler:</p>
<pre class="code-pre "><code langs="">bundle install
</code></pre>
<p>We need to disable the default Nginx configuration. Open the Nginx config file:</p>
<pre class="code-pre "><code langs="">sudo nano /etc/nginx/sites-available/default
</code></pre>
<p>Find the lines:</p>
<pre class="code-pre "><code langs="">listen 80 default_server;
listen [::]:80 default_server ipv6only=on;
</code></pre>
<p>Comment them out, like this:</p>
<pre class="code-pre "><code langs=""># listen 80 default_server;
# listen [::]:80 default_server ipv6only=on;
</code></pre>
<p>Save the file.</p>

<p>Now, create an Nginx configuration file for our app:</p>
<pre class="code-pre "><code langs="">sudo nano /etc/nginx/sites-available/testapp
</code></pre>
<p>Add the following <code>server</code> block. The settings are explained below.</p>
<pre class="code-pre "><code langs="">server {
  listen 80 default_server;
  server_name <span class="highlight-key-word">www.mydomain.com</span>;
  passenger_enabled on;
  passenger_app_env development;
  root <span class="highlight-key-word">/home/rails/testapp/public</span>;
}
</code></pre>
<p>In this file, we enable listening on port 80, set your domain name, enable Passenger, and set the root to the <em>public</em> directory of our new project. The <span class="highlight-key-word">root</span> line is the one you'll want to edit to match the upload location of your Rails app.</p>

<p>If you don't want to assign your domain to this app, you can skip the <span class="highlight-key-word">server_name</span> line, or use your IP address.</p>

<p>To test our setup, we want to see the Rails <strong>Welcome aboard</strong> page. However, this works only if the application is started in the development environment. Passenger starts the application in the production environment by default, so we need to change this with the <code>passenger_app_env</code> option. If your app is ready for production you'll want to leave this setting out.</p>

<p>Save the file (<strong>CTRL+x</strong>, <strong>y</strong>, <strong>ENTER</strong>).</p>

<p>Create a symlink for it:</p>
<pre class="code-pre "><code langs="">sudo ln -s /etc/nginx/sites-available/testapp /etc/nginx/sites-enabled/testapp
</code></pre>
<p>Restart Nginx:</p>
<pre class="code-pre "><code langs="">sudo nginx -s reload
</code></pre>
<p>Now your app's website should be accessible. Navigate to your Droplet's domain or IP address:</p>
<pre class="code-pre "><code langs="">http://droplet_ip_address
</code></pre>
<p>And verify the result:</p>

<p class="growable"><img src="https://assets.digitalocean.com/articles/Rails_Passenger_Nginx/3.png" alt="Test page"></p>

<p>You should see the Rails test app live on your server.</p>

<div name="step-eight-—-update-regularly" data-unique="step-eight-—-update-regularly"></div><h2 id="step-eight-—-update-regularly">Step Eight — Update Regularly</h2>

<p>To update Ruby, you will need to compile the latest version as shown in Step Four in this tutorial.</p>

<p>To update Passenger with Nginx, you will need to run a basic system update:</p>
<pre class="code-pre "><code langs="">sudo apt-get update &amp;&amp; sudo apt-get upgrade
</code></pre>
<p>However, if there is a new system Ruby version available, it will probably overwrite our Ruby (installed from source). For this reason, you might need to re-run the commands for removing the existing version of Ruby and creating a new symlink to the Ruby binary file. They are listed at the end of Step Five in this tutorial.</p>

<p>After the update process, you will need to restart the web server:</p>
<pre class="code-pre "><code langs="">sudo service nginx restart
</code></pre>
    </div>