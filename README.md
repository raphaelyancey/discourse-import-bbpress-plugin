# What is this

This set of rake tasks imports users, threads, and posts from a bbPress
instance into Discourse. This is an update to [Wayne Graham's](https://github.com/waynegraham/discourse-import-bbpress) plugin that was built specifically for a bbPress standalone instance. My update is works specifically with the bbPress Plugin for Wordpress. bbPress standalone has been deprecated for some time now.

* Post dates and authors are preserved
* User accounts are created from bbPress, but have no login
  capabilities. The emails addresses are set, and gravatars work, but
users will need to log in to activate their accounts.
* There is a `test` mode to test the connection to the mysql server and
  read posts

**Use at your own risk!** Seriously, test this on a dummy Discourse
installation first.

# Instructions
  
* *Run these commands as the discourse user `sudo su - discourse`*
  
* Head over to the discourse directory `cd /var/www/discourse`

* **Important:** *disable* your email configuration or you will spam all your users with hundreds of emails.

  Add this to your environment config: `config/environments/development.rb`

  ```ruby
  config.action_mailer.delivery_method = :test
  config.action_mailer.smtp_settings = { address: "localhost", port: 1025 }
  ```

  Install and start `mailcatcher` to monitor the sending of notification
  emails:

  As a general practice, it's a good idea to run ```gem update --system``` and ```gem update``` before installing additional gems.

  ```shell
  $ gem install mailcatcher
  $ mailcatcher --http-ip 0.0.0.0
  ```

  If you get errors like ```unable to convert "\x89" from ASCII-8BIT to UTF8``` you need to install a version of the rdoc gem that supports the conversion.

  ```
  $ gem install rdoc
  $ gem rdoc --all --overwrite
  ```

* Be sure to have at least one user in your Discourse instance. If not,
  create one and set the username in `config/import_bbpress.yml`. Details on how to set up an Administrator account can be found [here](https://github.com/discourse/discourse/blob/master/docs/INSTALL-ubuntu.md#administrator-account)

* Edit `config/import_bbpress.yml` with your database connection
  information and `discourse_admin` username.

* Install the mysql gem: `gem install mysql2`

  **Note:** You may need to install the header files for mysql.
  * For **OS X**,
you can do this with `brew install mysql`;
  * For **Debian**, `sudo apt-get
install libmysqlclient-dev`;
  * For **CentOS/RHEL**, `sudo yum install
mysql-devel`.

* Copy `config/import_bbpress.yml` to your `discourse/config` directory
* Copy `lib/tasks/import_bbpress.rake` to your `discourse/lib/tasks/`
  directory
* In your discourse instance, run `rake import:bbpress`

  **Note:** if you are running multisite, you will need pass your database
instance: `export RAILS_DB=<your_database> rake import:bbpress`

* Cross your fingers
* If everything worked, deploy
* Be sure to have your users reset their passwords on the new Discourse
  site.
  
# Extra
* I encountered issues running the importer due to the **scrub** function patch in `lib/freedom_patches/scrub.rb`. Just in case you do run into a scrub error, modify this file to run the import then revert back after your done. I just changed `def scrub` to `def scrub2`.
* If you run into `"You are trying to install in deployment mode after changing your Gemfile."` errors, just follow the instructions given. Run `bundle install` elsewhere and add the Gemfile.lock to version control.


# Contributing

Please make all pull requests to the develop branch. And example process
for making a pull request:

1. Fork and clone the repo
1. `$ git checkout -b feature-my-awesome-improvement`
1. Make changes and commit
1. Push changes to Github
1. Create a **pull request** from `feature-my-awesome-improvement` on
   your repo to `develop` on the main repo
1. Celebrate your contribution to Open Source!!
