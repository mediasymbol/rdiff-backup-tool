# Installation
1. Clone this repository to your target and backup machines

    `git clone --depth=1 https://github.com/mediasymbol/rdiff-backup-tool.git`

2. Depending on which backup mode (push/pull) you want to use, create `docker-compose.yml` files from the corresponding templates.

   Client is an 'active' agent that runs crond service, connects to the server, pushes or pulls backups and restores the data.

   Bind `config`, `backup`, and `restore` folders:
   + `config` - for container configuration files
   + `backup` - used for data that you want to backup and for storing backups on the other side
   + `restore` - must be bound only on your target machine (client for push mode, server for pull mode) and will be used for the restored data

3. Run `bin/rdiff init` on both machines. The simple prompt will guide you through the container initialization process.

4. After successful initialization of the containers you need to copy the client public key `/config/.ssh/ssh_client_rsa_key.pub` to your server machine **using the same path and file name**.

   This key is needed to configure the `authorized_keys` file. The server container will not start without it.

5. Run `bin/rdiff start` on your server and **then** on client. The client downloads the server public key and will not start if server isn't online.

6. Additionally you can configure the `/config/backup.list` file on your client machine to define precisely which data should be included or excluded from backup (see rdiff-backup `--include-globbing-filelist` option)

# Environment variables
Server has no environment settings.

Client **requires** following variables:
1. SERVER_HOST - URL of your server machine (e.g. your-server.tld)
2. SERVER_PORT - SSH port number of the server (e.g. 22)
3. BACKUP_FREQUENCY - defines how often the backup script will be executed. Possible values are `15min`, `daily`, `hourly`, `monthly` and `weekly`
4. BACKUP_EXPIRE - defines how long the backup increments will be stored (see rdiff-backup time formats and `--remove-older-than` option). Older increments will be deleted automatically.

It is also possible to enable reporting via email. This requires `MAIL_ENABLED=1` combined with following settings (see also SSMTP configuration file):
1. MAIL_SENDER_ADDRESS (e.g. _backup@your-mail.tld_)
2. MAIL_SERVER_HOST (e.g. _mail.your-mail.tld_)
3. MAIL_SERVER_PORT (e.g. _587_)
4. MAIL_AUTH_USER (e.g. _backup@your-mail.tld_)
5. MAIL_AUTH_PASS (e.g. __backup_mail_user_password_\_)
6. MAIL_RECEIVER_ADDRESS (e.g. admin@your-mail.tld)
7. MAIL_TLS (e.g. _YES_)
8. MAIL_STARTTLS (e.g. _YES_)

# `bin/rdiff` script
Besides the already mentioned commands you can also do several other things with the help of this script:

* Start, stop, (re-)initialize container or print its logs.
* Run backup process manually
* Show the list of all available increments
* Restore either all data or specific file/directory as it was at given time

Simply run `bin/rdiff` to see the list of available commands.

# Docker image
The sources for the used docker image can be found in the [mediasymbol/rdiff-backup](https://github.com/mediasymbol/rdiff-backup) project
