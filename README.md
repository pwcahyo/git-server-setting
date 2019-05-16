## Setting server gitlab
1. Pertama kali harus menyiapkan user `git` dan `.ssh` directory
```
$ sudo adduser git
$ su git
$ cd
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

2. Menambahkan SSH Public Keys user yang akan melakukan akses `(pull,commit,push)` kedalam `authorized_keys`
- contoh isi SSH public keys
```
$ cat /tmp/id_rsa.john.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCB007n/ww+ouN4gSLKssMxXnBOvf9LGt4L
ojG6rs6hPB09j9R/T17/x4lhJA0F3FR1rP6kYBRsWj2aThGw6HXLm9/5zytK6Ztg3RPKK+4k
Yjh6541NYsnEAZuXz0jTTyAUfrtU3Z5E003C4oxOj6H0rfIF1kKI9MAQLMdpGW1GYEIgS9Ez
Sdfd8AcCIicTDWbqLAcU4UpkaX8KyGlLwsNuuGztobF8m72ALC/nLF6JLtPofwFBlgc+myiv
O7TCUSBdLQlgMVOFq1I2uPWQOkOWQAHukEOmfjy2jctxSDBQ220ymjaNsHT4kgtZg2AYYgPq
dAv8JggJICUvax2T9va5 gsg-keypair
```
- penambahan banyak file SSH public keys :
```
$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
$ cat /tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys
$ cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys
```

3. Membuat direktori diserver digunakan sebagai akses git-server
```
$ mkdir project.git
$ cd project.git
$ git init --bare
Initialized empty Git repository in /project.git/
```


4. Melalukan setting hooks untuk mengarahkan kode yang sudah dilakukan `(pull,commit,push)` agar masuk kedalam direktori lain
- ciptakan file `post-receive` didalam folder hooks, kemudian isikan kode bash untuk destinasi work-tree
```
#!/bin/bash
GIT_WORK_TREE=/home/git/repo/project/ git checkout -f master
chmod -R 775 /home/git/repo/project/
```
* note : chmod untuk mengubah permission file didalam folder agar bisa executable

5. Ubah permission `post-receive`  menjadi executable
```
chmod +x post-receive
```

6. 