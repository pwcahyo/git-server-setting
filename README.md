## Setting server gitlab
#### 1. Pertama kali harus menyiapkan `git` user dan `.ssh` directory
```
$ sudo adduser git
$ su git
$ cd
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

#### 2. Menambahkan SSH Public Keys user yang akan melakukan akses `(pull,commit,push)` kedalam `authorized_keys`
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

#### 3. Membuat direktori diserver digunakan sebagai akses git-server
```
$ mkdir project.git
$ cd project.git
$ git init --bare
Initialized empty Git repository in /project.git/
```


#### 4. Melalukan setting hooks untuk mengarahkan kode yang sudah dilakukan `(pull,commit,push)` agar masuk kedalam direktori lain `/home/git/repo/project/ adalah direktori repo original`
- contoh letak lokasi direktori `/home/git/repo/project/` ubah kepemilikan direktori sesuai user `git`
```
chown -R git:git /home/git/repo/project/
```
- ciptakan file `post-receive` didalam folder hooks, kemudian isikan kode bash untuk destinasi work-tree
```
#!/bin/bash
GIT_WORK_TREE=/home/git/repo/project/ git checkout -f master
chmod -R 775 /home/git/repo/project/
```
* note : chmod untuk mengubah permission file didalam folder agar bisa executable

#### 5. Ubah permission `post-receive`  menjadi executable
```
chmod +x post-receive
```

#### 6. Untuk membatasi agar user git tidak bisa melakukan akses SSH di terminal
```
$ cat /etc/shells   # see if `git-shell` is already in there.  If not...
$ which git-shell   # make sure git-shell is installed on your system.
$ sudo -e /etc/shells  # and add the path to git-shell from last command
$ chsh git -s $(which git-shell) 
```
apabila berhasil maka akan muncul seperti ini
```
$ ssh git@gitserver
fatal: Interactive git shell is not enabled.
hint: ~/git-shell-commands should exist and have read and execute access.
Connection to gitserver closed.
```
untuk mengembalikan shell ke bash terminal semula
```
$ chsh git -s /bin/bash
```

## Setting Client
copy repo disediakan dua cara, yaitu dengan cara clone dan remote

#### 1. Remote
```
$ mkdir myproject
$ cd myproject
$ git init
$ git add .
$ git commit -m 'initial commit'
$ git remote add origin git@gitserver:/srv/git/project.git
$ git push origin master
```

#### 2. Clone
```
$ git clone git@gitserver:/srv/git/project.git
$ cd project
$ nano README
$ git commit -am 'fix for the README file'
$ git push origin master
```

## SSH Setting
Agar virtual environment aktif saat user SSH login maka dapat ditambahkan script berikut didalam `.bashrc`
```
# virtual environment cppbt run
if [[ -n $SSH_CONNECTION ]] ; then
    conda activate cppbt
    cd /home/git/repo/cpb
fi
```

* note : `cd /home/git/repo/cpb` digunakan untuk setelah bisa berhasil login akan diarahkan ke lokasi tersebut

### Selesai
