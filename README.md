# autobarebackup
just a really simple incremental backup script for linux using tar and pigz. I created this for bacula because if I have a lot of files the bacula db could grow really big.


### usage
first create the /backup/$namespace ($DIR_BACKUP_BASE, $namespace is for multiple filesets) 
write the directory list in the/backup/$namespace/backup.include (tar -T format)
you can also create an exclude list: /backup/$namespace/backup.exclude (--exclude-from format)

Then just run the autobarebackup script and if you want, create a config file also for email alert, parallel gzip etc
you can just create a cronjob for this.
```
/usr/local/bin/autobarebackup [option]
        -h: show help
        -q: silent mode
        -c <config>: read a config file
```
## example directory tree after the backup
```
/backup/
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
