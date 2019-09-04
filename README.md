# Probably a stupid idea.

This is a proof of concept of a development environment for Drupal completely docker based using 3 containers:

* Apache
    * PHP 7.2
    * Composer
    * Drush
* MariaDB
    * Minor custom settings for Drupal optimization
* Coder
    * Web based version of Microsoft visual studio code, based on https://github.com/cdr/code-server
    * Uses as ZSH as shell
    * Git
    * Can make calls to the other containers.

...and a `Docker volume`, this is to speed up performance and this should work at the same speed on Linux, MacOS and Windows. WARNING! If you remove the volume you remove your code!


## Requirements

* Docker
* Docker compose


## Step 1 - Start the environment.

1. Clone this repository somewhere to your hardrive.

    ```bash
    $ git clone git@github.com:pixelpiloten/coder_drupal.git myfolder
    ```

2. Start the environment.

    ```bash
    $ docker-compose up --build --force-recreate -d
    ```


## Step 2 - Install drupal.

1. Open Microsoft Visual studio code in the browser using this url.

    `http://127.0.0.1:8443`

2. Click on `View` in the menu and choose `Terminal` to open the terminal.

3. Download Drupal using Composer to the current directory (this will actually exec composer in the apache container).

    ```bash
    $ composer create-project drupal-composer/drupal-project:8.x-dev . --no-interaction
    ```

4. OPTIONAL: If you get an error about `...requires behat/mink-selenium2-driver 1.3.x-dev -> no matching package found` then add this to the `composer.json` file under `require`.

    `"behat/mink-selenium2-driver": "dev-master as 1.3.x-dev"`

5. OPTIONAL: Run composer to update your packages if you got the above error.

    ```bash
    $ commposer update
    ```

6. Install Drupal with Drush.

    ```bash
    $ drush si --db-url=mysql://root:password@mariadb/drupal --site-name=MySite
    ```

7. Create a one time login link to login to Drupal admin.

    ```bash
    $ drush uli
    ```


## Custom commands

All commands live in the `.docker/coder/config/zshrc_aliases` file, now this is very basic and not the most intuitive way but this is a proof of concept so just add what you want and rebuild the containers.

```bash
alias php="docker exec -u 1000:1000 -t drupal_apache php"
alias composer="docker exec -u 1000:1000 -t drupal_apache composer"
alias drush="docker exec -u 1000:1000 -it drupal_apache drush --root /home/coder/project/code-server/web --uri 127.0.0.1:8080"
```