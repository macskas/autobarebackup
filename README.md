# autobarebackup
just a really simple incremental backup script for linux using tar and pigz or mksquashfs. I created this for bacula because if I have a lot of files the bacula db could grow really big.


## usage
first create the /backup/$namespace ($DIR_BACKUP_BASE, $namespace is for multiple filesets) 
write the directory list in the/backup/$namespace/backup.include (tar -T format)
you can also create an exclude list: /backup/$namespace/backup.exclude (--exclude-from format)
you can optinally define the backup method in /backup/$namespace/backup.method (valid options: tar or mksquashfs). 

### if the backup.method == "squashfs"
If you are using squashfs it wont be incremental, but it will be deduplicated and compressed.

### if the backup.method == "tar"
Your backup will be an incremental backup. By default a full backup will run on sunday and incremental after that. Of course you can change this in the config.

Then just run the autobarebackup script and if you want, create a config file also for email alert, parallel gzip etc
you can just create a cronjob for this.
```
/usr/local/bin/autobarebackup [option]
        -h: show help
        -q: silent mode
        -u: update from github (master)
        -c <config>: read a config file
```
### example directory tree after the backup
```
/backup/
/backup/www
/backup/www/backup.include
/backup/www/backup.exclude
/backup/www/backup.method
/backup/www/full
/backup/www/full/2016-02-25
/backup/www/full/2016-02-25/2016-02-25_21h29m_Thu-_www_.squashfs
/backup/www/latest
/backup/www/latest/2016-02-25
/backup/misc
/backup/misc/latest
/backup/misc/latest/2016-02-14
/backup/misc/backup.exclude
/backup/misc/backup.include
/backup/misc/incr
/backup/misc/incr/2016-02-14
/backup/misc/incr/2016-02-14/2016-02-19_23h21m_Fri.tar.gz
/backup/misc/incr/2016-02-14/files.snar
```

### bacula
example bacula fileset config (/etc/bacula/conf.d/fileset/AutoBareMetalBackupFiles.conf)
```
FileSet {
  Name = "AutoBareMetalBackupFiles"
  Include {
    Options {
        signature = MD5
    }
    File = "\\|/usr/bin/find /backup/ -type l -wholename \"*/latest/*\" -printf \"%h/%f/.\\n\""
  }
}
```
