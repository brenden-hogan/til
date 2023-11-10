This is a basic s3 restic backup setup specific to OSX but should be easily set up for other OS's.

### Set up a repository

https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#amazon-s3

Make sure to store all the access info/passwords somewhere safe.

### Set up a cron
```
crontab -e
```
```
RESTIC_REPOSITORY="s3:s3.amazonaws.com/<bucket>"
RESTIC_PASSWORD_FILE="<path>"
AWS_ACCESS_KEY_ID="<secret>"
AWS_SECRET_ACCESS_KEY="<secret>"
AWS_DEFAULT_REGION="<region>"

*/30 * * * * /users/<userName>/backup_script.sh  >> <path>/backup_cron.log 2>> <path>/backup_cron.log
```
All of this info should have been obtained when setting up your repository. Ensure all files are protected with the appropriate permissions.

You may also want to set up periodic clean up of the log file.

Example Backup Script
```shell
/opt/homebrew/bin/restic backup /Users/<userName> --exclude-file=<path>/excludes.txt
/opt/homebrew/bin/restic forget --keep-daily 45
/opt/homebrew/bin/restic prune
```
Make sure to add any other directories that aren't in your main user folder

### Excludes File
Excludes file uses glob syntax https://restic.readthedocs.io/en/stable/040_backup.html#excluding-files

Example Excludes File
```
**/out/**
**/target/**
**/.unison/**
**/env/**
**/build/**
**/node_modules/**
**/.cargo/**
**/.gradle/**
**/.npm/**
**/.runtimes/**
**/.rustup/**
**/.sdkman/**
**/.m2/**
**/.local/**
**/.asdf/**
**/.vscode/**
**/.cisco/**
/Users/<userName>/cache/**
Pictures/Photos Library.photoslibrary/**
/Users/<userName>/Library/Caches/**
/Users/<userName>/Library/Application Support/**
/Users/<userName>/Library/Containers/**
/Users/<userName>/Library/Group Containers/**
```
