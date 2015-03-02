Detroit Ledger: Drupal install profile
======================================

This Drupal install profile is used by detroitledger.org.

Docker install
--------------

Use Docker to easily roll out the site. See `docker-deploy.sh`.

Local install
-------

With PHP>5.4 and Drush 6:

```
drush make --working-copy https://raw.githubusercontent.com/detroitledger/gnl_profile/boardmembers/distro.make gnl_webroot
cd gnl_webroot
mysql -uroot -e 'create database gnl'
cat>>sites/default/settings.php<<'EOF'
<?php
$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'gnl',
  'username' => 'root',
  'password' => '',
  'host' => 'localhost',
);
EOF
# if you're on OS X using homebrew mysql, specify
#   'unix_socket' => '/tmp/mysql.sock',
# instead of
#   'host' => 'localhost',
ssh -A root@data.detroitledger.org 'ssh `docker inspect --format="{{.NetworkSettings.IPAddress}}" gnl-www` "cd /srv/www/drupal; drush sql-dump"' | drush sqlc
drush rs
```

On OS X, do this beforehand:

```
brew tap homebrew/homebrew-php
brew update
brew install php56
brew install --ignore-dependencies composer
composer global require drush/drush:6.*
# add export PATH="$PATH:~/.composer/vendor/bin" to .bashrc
# also, install mysql server like so: http://stackoverflow.com/a/6378429
```

Test
----

A test suite lives in `tests/`. Tests are run using [PhantomJS](http://phantomjs.org/), [Behat](http://behat.org), [Mink Extension](http://extensions.behat.org/mink/), and [Drupal Extension](https://github.com/jhedstrom/drupalextension).

After you do everything in the Install section above, you can run tests! Make sure the server is running on port 8080.

```
# install phantomjs
# npm install -g phantomjs works well!
# run phantomjs:
phantomjs --webdriver=8643 &
# set up behat/mink
cd profiles/gnl_profile/tests
curl -s https://getcomposer.org/installer | php
php composer.phar install # you need the php5-curl extension, among other extensions!
# make sure behat has the drupalextension step definitions.. if nothing shows up, run:
#./bin/behat --init
./bin/behat -dl
# run tests
./bin/behat features/
```

[API Documentation](http://docs.detroitledger.apiary.io/)
------------------

[API documentation is available on Apiary](http://docs.detroitledger.apiary.io/).



More information
----------------

* http://detroitledger.org/
* https://twitter.com/detroitledger
* http://detroitledger.tumblr.com/

Credits
-------

Benjamin Chodoroff, Matthew Hampel, Jessica McInchak.

License
-------

Copyright 2013, Benjamin Chodoroff, Matthew Hampel, Jessica McInchak. This software is distributed under the terms of the GNU General Public License.
