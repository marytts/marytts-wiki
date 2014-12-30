# Install MaryTTS to a local server

This recipe documents how to install MaryTTS to a local server, e.g., for projects relying on a TTS service or enterprise-wide deployment.

## Preliminaries

In this recipe, we will use MaryTTS v5.1-beta2 on a vanilla Ubuntu 12.4.4 LTS server.
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
$ sudo -u mary git checkout v5.1beta2
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
$ sudo -u mary /local/mary/marytts/target/marytts-5.1-beta2/bin/marytts-server.sh
```
Of course a proper init script would be nice... [Here's one](https://gist.github.com/psibre/3ccd5356a63cc3a78dc7) that was used on the old, retired demo server.