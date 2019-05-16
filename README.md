## Setting server gitlab
1. Pertama kali harus menyiapkan user `git` dan `.ssh` directory
```
$ sudo adduser git
$ su git
$ cd
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

