# Install MaryTTS to a local server

This recipe documents how to install MaryTTS to a local server, e.g., for projects relying on a TTS service or enterprise-wide deployment.

## Preliminaries

In this recipe, we will use MaryTTS v5.2 on a vanilla Ubuntu 12.4.4 LTS server.
We will create a dedicated service account named `mary` to manage the installation files and run the service.
Moreover, we will place the MaryTTS installation in `/local/mary/marytts` and serve the documentation from `/local/mary/www`.
The MaryTTS server itself will run on the default port 59125.

```bash
$ sudo useradd -m -r mary
$ sudo mkdir -p /local/mary
$ sudo chown mary:mary /local/mary
```

## Download the MaryTTS source code

The best way to obtain the source code is to clone it from GitHub using `git` (which might need to be installed first).

```bash
$ sudo apt-get install -y git
$ sudo -u mary git clone https://github.com/marytts/marytts.git /local/mary/marytts
$ cd /local/mary/marytts
$ sudo -u mary git fetch --tags
$ sudo -u mary git checkout v5.2
```

## Build MaryTTS

(JDK 7 and Maven might need to be installed first.)

```bash
$ sudo apt-get install -y openjdk-7-jdk maven
$ sudo -u mary mvn package
```

## Build MaryTTS website (optional)

The MaryTTS artifacts need to be installed in the local Maven repository first.

```bash
$ sudo -u mary mvn install
$ sudo -u mary mvn site:site site:stage -DstagingDirectory=/local/mary/www
```

We will use `nginx` for this and configure it to serve the MaryTTS website.
```bash
$ sudo apt-get install -y nginx
```

Add a configuration file to `/etc/nginx/sites-available` with contents like this:
```nginx
server {
  location / {
    alias /local/mary/www/;
  }
}
```
Then create a symbolic link to that file in `/etc/nginx/sites-enabled`

Finally, start `nginx`:
```bash
$ sudo service nginx start
```

## Run the MaryTTS HTTP server

We can start the MaryTTS server as an HTTP server.
```bash
$ sudo -u mary /local/mary/marytts/target/marytts-5.2/bin/marytts-server
```
Of course a proper init script would be nice... [Here's one](https://github.com/marytts/marytts/blob/e8384220f9308a0b660f72df4c90ab7f88feb06d/marytts-assembly/assembly-runtime/src/runtime/doc/examples/etc_init.d_marytts) that was used on the old, retired demo server.

## Setting up the cron job

The idea of this part is to monitor any crash of the marytts server (memory leak, ...) and restart it when necessary.

1. Move the `mary.sh` to a directory and remember the path.
    ```bash
    #!/bin/bash
    
    # Version to adapt to your system
    VERSION=5.2
    
    #Check if our service is currently running
    ps auxw | grep marytts-server | grep -v grep
    
    # if the service is not running it returns a non zero to the environment viriable,
    # in that case we start the service, else we ignore.
    if [ $? != 0 ]
    then
        bash /local/mary/marytts/target/marytts-$VERSION/bin/marytts-server -Xmx2g
    fi
    ```
2. Open `mary.sh` and edit path to `marytts-server` if different from the default.
3. Give `mary.sh` execute permission with the command
    ````
    chmod +x mary.sh
    ````
    (One might be asked for super user)
4. Open a terminal and setup a cron job with the command `crontab -e` (for user specific job) or `sudo crontab -e` (for system wide job)
5. Go to the end of the file and add the following line
    ```
    */30 * * * * /PATH/TO/mary.sh
    ```
    (This is set to 30 minutes interval).
    For other time intervals
    ```
    * * * * *  command to execute (like above)
    ┬ ┬ ┬ ┬ ┬
    │ │ │ │ │
    │ │ │ │ │
    │ │ │ │ └───── day of week (0 - 7) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
    │ │ │ └────────── month (1 - 12)
    │ │ └─────────────── day of month (1 - 31)
    │ └──────────────────── hour (0 - 23)
    └───────────────────────── min (0 - 59)
    ```
    
    Save the crontab and exit.
