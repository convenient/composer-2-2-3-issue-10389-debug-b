Additional case for https://github.com/composer/composer/issues/10389

To reproduce I have created two repositories
1. one with a simple script which should be in `./vendor/bin/script.sh`, see https://github.com/convenient/composer-2-2-3-issue-10389-debug-a/blob/main/script.sh
2. one which pulls in this simple script, and tries to source it rather than running it directly

## To reproduce

Clone this repo
```
git clone https://github.com/convenient/composer-2-2-3-issue-10389-debug-b
cd composer-2-2-3-issue-10389-debug-b
composer install
```

See that you can run the dummy script normally
```
$ ./vendor/bin/script.sh
This script is running
```

See that you cannot source the script

```
$ . ./vendor/bin/script.sh 
bash: cd: ../convenient/composer-2-2-3-issue-10389-debug-a: No such file or directory
bash: /script.sh: No such file or directory

$ source ./vendor/bin/script.sh 
bash: cd: ../convenient/composer-2-2-3-issue-10389-debug-a: No such file or directory
bash: /script.sh: No such file or directory
```

See that the bash environment variable workaround (https://github.com/composer/composer/pull/10402) does not seem to work when you source the script, it also leaves a broken `$COMPOSER_BIN_DIR` afterwards because of it being sourced.

```
$ echo $COMPOSER_BIN_DIR
/Users/lukerodgers/src/composer-2-2-3-issue-10389-debug-b/vendor/bin
$ ./vendor/bin/script.sh 
This script is running

$ . ./vendor/bin/script.sh 
bash: cd: ../convenient/composer-2-2-3-issue-10389-debug-a: No such file or directory
bash: /script.sh: No such file or directory
$ echo $COMPOSER_BIN_DIR
/bin
```