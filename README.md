# WordPress-Plugin-Unit-Testing
How to Perform WordPress Plugin Testing on Windows Using WP-CLI and Local Environment

## Prerequisites

Before getting started, ensure you have the following tools installed:

- **Git**: Version control system.
- **WP-CLI**: Command-line tool for interacting with WordPress.
- **PHP**: Latest version compatible with WordPress.
- **Composer**: PHP dependency manager.
- **Node.js & npm**: Optional, but recommended for additional packages.
- **XAMPP/WAMP/MAMP**: Local server setup.

## Step 1: Set Up Your Local Development Environment

First, you'll need to set up a local environment to run WordPress on your Windows machine.

### Install XAMPP/WAMP

1. Download and install [XAMPP](https://www.apachefriends.org/index.html) or [WAMP](http://www.wampserver.com/en/).
2. Start **Apache** and **MySQL** services after installation.

### Download WordPress

You can manually download WordPress from [WordPress.org](https://wordpress.org/download/) or use WP-CLI for an easier installation.

```bash
wp core download
```
### Set Up a Local Database:
Use phpMyAdmin (included in XAMPP/WAMP) to create a database for your WordPress installation.

### Install WordPress:
Navigate to your local server folder (e.g., C:/xampp/htdocs/my-site) and install WordPress:

```bash
wp core config --dbname=my_database --dbuser=root --dbpass= --dbhost=localhost
wp core install --url=localhost/my-site --title="My Test Site" --admin_user=admin --admin_password=strongpassword --admin_email=admin@example.com
```
## Step 2: Install WP-CLI on Windows

WP-CLI is essential for managing your WordPress site from the command line. Here’s how you can install it on Windows.
### Download WP-CLI Phar:

Open your terminal (e.g., Git Bash or Command Prompt) and download the WP-CLI Phar file:
```bash
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
```
### Make WP-CLI Executable:

Add WP-CLI to your system's PATH to make it executable globally.
```bash
chmod +x wp-cli.phar
```
### Move WP-CLI to a Global Location:

Move the wp-cli.phar file to a global location, e.g., C:\wp-cli\wp.
```bash
mv wp-cli.phar /usr/local/bin/wp
```
### Test WP-CLI Installation:

Verify that WP-CLI is installed successfully by checking the version:
```bash
wp --info
```
## Step 3: Setting Up a WordPress Plugin for Testing

Once you have WordPress set up, the next step is creating or configuring a plugin for testing.
### Create a Plugin:

If you’re working on a new plugin, create a basic file structure for it in the ```wp-content/plugins/ directory```.
```bash
mkdir wp-content/plugins/my-plugintouch wp-content/plugins/my-plugin/my-plugin.php
```
###Activate the Plugin:

Activate your plugin using WP-CLI:
```bash
wp plugin activate my-plugin
```
## Step 4: Writing Unit Tests for WordPress Plugin

WordPress uses PHPUnit for running unit tests. Here’s how you can set it up for your plugin.

### Install PHPUnit:

If you haven’t installed PHPUnit yet, use Composer to set it up:
```bash
composer require --dev phpunit/phpunit ^7
```
### Set Up the Testing Environment:

WordPress provides an ```install-wp-tests.sh``` script to set up a testing environment.
First, download the script to your plugin folder:

```bash
curl -o install-wp-tests.sh https://raw.githubusercontent.com/wp-cli/wp-cli-tests/master/bin/install-wp-tests.shchmod +x install-wp-tests.sh
```
### Run the Script:

Run the script to create a test database and install the necessary dependencies.
```bash
bash install-wp-tests.sh my_database root '' localhost latest
```
### Write a Test Case:

Create a new test file in your plugin directory, e.g., tests/test-sample.php.
```php
<?phpclass SampleTest extends WP_UnitTestCase {   function test_sample() {      $this->assertTrue( true );   }}
```
### Run the Tests:

Execute your tests using the following command:
```bash
phpunit
```
If everything is set up correctly, ```PHPUnit``` will run and display the results of your tests.

## Step 5: Automating Tests with Continuous Integration

To ensure that your plugin is always working across environments, it's a good practice to automate testing using Continuous Integration (CI). 
Here’s a basic outline for setting up GitHub Actions for your plugin.

### Create a GitHub Repository:

Push your plugin code to a new GitHub repository.
Set Up GitHub Actions:In your GitHub repository, create a ```.github/workflows/phpunit.yml``` file with the following content:

```yaml
name: PHPUnit Testson: [push, pull_request]jobs:  phpunit:    runs-on: ubuntu-latest    services:      mysql:        image: mysql:5.7        env:          MYSQL_ROOT_PASSWORD: root          MYSQL_DATABASE: wordpress_tests        ports:          - 3306:3306        options: >-          --health-cmd="mysqladmin ping --silent"          --health-interval=10s          --health-timeout=5s          --health-retries=3    steps:    - name: Checkout code      uses: actions/checkout@v2    - name: Set up PHP      uses: shivammathur/setup-php@v2      with:        php-version: 7.4    - name: Install dependencies      run: composer install    - name: Run PHPUnit      run: phpunit
```
### Push Changes to GitHub:

Once this is set up, GitHub Actions will automatically run your tests every time you push new code or create a pull request.

## Step 6: Troubleshooting and Debugging

If you encounter any issues during testing, here are some tips:
Check PHP Error Logs:Ensure that WP_DEBUG is enabled in your ```wp-config.php``` file for error logging:
```php
define( 'WP_DEBUG', true );define( 'WP_DEBUG_LOG', true );
```
### Check Plugin Dependencies:

Ensure all dependencies (e.g., required PHP or WordPress versions) are installed and active.
### Re-run Database Setup:
If tests fail due to database errors, try re-running the ```install-wp-tests.sh``` script to ensure the testing database is correctly set up.

## Conclusion
Testing WordPress plugins on Windows involves setting up a local development environment, configuring **WP-CLI**, and using **PHPUnit** for automated testing. 
With these steps, you can ensure your plugin adheres to WordPress standards and performs optimally. 
Continuous integration tools like **GitHub Actions** further automate the testing process, ensuring quality control across different environments.
