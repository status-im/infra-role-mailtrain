# Description

This role deploys an instance of [MailTrain](https://github.com/Mailtrain-org/mailtrain) - a self-hosted newsletter app.

# Configuration

Basic config should include:
```yaml
# DB
mailtrain_db_pass: 'super-secret-db-pass'
mailtrain_db_root_pass: 'super-secret-db-root-pass'
mailtrain_cookie_secret: 'super-secret-cookie-secret'
# Mail
mailtrain_default_sender: 'news@example.org'
mailtrain_default_from: 'Example News'
mailtrain_default_address: 'noreply@example.org'
mailtrain_smtp_host: 'smtp.example.org'
mailtrain_smtp_port: 587
mailtrain_smtp_user: 'smtp-user'
mailtrain_smtp_pass: 'smtp-password'
mailtrain_smtp_encryption: 'STARTTLS'
# OAuth
mailtrain_oauth_gh_org: 'my-gh-org'
mailtrain_oauth_gh_teams: ['newsletter-senders']
mailtrain_oauth_id: '123qwe123qwe'
mailtrain_oauth_secret: '123qwe123qwe123qwe123'
mailtrain_oauth_cookie_secret: '123qwe'
# S3cmd uploads
mailtrain_s3cmd_bucket_name: 'my-s3-bucket'
mailtrain_s3cmd_encryption_pass: 'super-secret-password'
mailtrain_s3cmd_access_key: 's3-access-key'
mailtrain_s3cmd_secret_key: 's3-secret-key'
```

# Management

Serivce is deployed using [Docker Compose](https://docs.docker.com/compose/) and can be managed using it:
```
/docker/mailtrain % docker-compose ps    
     Name                    Command               State                 Ports              
--------------------------------------------------------------------------------------------
mailtrain-app     node index.js                    Up      0.0.0.0:3000->3000/tcp           
mailtrain-cache   redis-server                     Up      6379/tcp                         
mailtrain-db      docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp, 33060/tcp
mailtrain-oauth   /bin/oauth2_proxy --email- ...   Up      127.0.0.1:8093->8093/tcp
```

# Backups

Backups are done using a Systemd timer:
```
 > s list-timers -a '*mailtrain.timer'  
NEXT                        LEFT          LAST PASSED UNIT                   ACTIVATES               
Fri 2020-12-11 00:00:00 UTC 5h 47min left n/a  n/a    backup-mailtrain.timer backup-mailtrain.service

 >  % ls -l /var/backups/mailtrain
total 44
-rw-r--r-- 1 root root 43115 Dec 10 18:11 mailtrain_db_dump_20201210181119.sql
```
