Cloner le projet dans le répertoire souhaité.
cd docker-magento/compose
Lancer la commande ../lib/onelinesetup 

Xdebug & PHPStorm
First, install the Chrome Xdebug helper. After installed, right click on the Chrome icon for it and go to Options. Under IDE Key, select PHPStorm from the list and click Save.

Next, enable Xdebug in the PHP-FPM container by running: bin/xdebug enable, the restart the docker containers (CTRL+C then bin/start).

Then, open PHPStorm > Preferences > Languages & Frameworks > PHP and configure:

CLI Interpreter

Create a new interpreter and specify From Docker, and name it markoshust/magento-php:7-2-fpm.
Choose Docker, then select the markoshust/magento-php:7-2-fpm image name, and set the PHP Executable to php.
Path mappings

Don't do anything here as the next Docker container step will automatically setup a path mapping from /var/www/html to ./src.
Docker container

Remove any pre-existing volume bindings.
Ensure a volume binding has been setup for Container path of /var/www/html mapped to the Host path of ./src.
Open PHPStorm > Preferences > Languages & Frameworks > PHP > Debug and set Debug Port to 9001,9003.

Open PHPStorm > Preferences > Languages & Frameworks > PHP > DBGp Proxy and set Port to 9001.

Open PHPStorm > Preferences > Languages & Frameworks > PHP > Servers and create a new server:

Set Name and Host to your domain name (ex. magento.test)
Keep port set to 80
Check the Path Mappings box and map src to the absolute path of /var/www/html
Go to Run > Edit Configurations and create a new PHP Remote Debug configuration by clicking the plus sign and selecting it. Set the Name to your domain (ex. magento.test). Check the Filter debug connection by IDE key checkbox, select the server you just setup, and under IDE Key enter PHPSTORM. This IDE Key should match the IDE Key set by the Chrome Xdebug Helper. Then click OK to finish setting up the remote debugger in PHPStorm.

Open up src/pub/index.php, and set a breakpoint near the end of the file. Go to Run > Debug 'magento.test', and open up a web browser. Ensure the Chrome Xdebug helper is enabled by clicking on it > Debug. Navigate to your Magento store URL, and Xdebug within PHPStorm should now trigger the debugger and pause at the toggled breakpoint.

Blackfire.io
These docker images have built-in support for Blackfire.io. To use it, first register your server ID and token with the Blackfire agent:

bin/root blackfire-agent --register --server-id={YOUR_SERVER_ID} --server-token={YOUR_SERVER_TOKEN}
Next, open up the bin/start helper script and uncomment the line:

#bin/root /etc/init.d/blackfire-agent start
Finally, restart the containers with bin/restart. After doing so, everything is now configured and you can use a browser extension to profile your Magento store with Blackfire.
