### Ставим необходимый софт
* yum install -y \redhat-lsb-core \wget \rpmdevtools \rpm-build \createrepo \yum-utils
### качаем исходник nginx и openssl
* yumdownloader --source nginx
* yumdownloader --source openssl
### Ставим пакеты из исходников
* rpm -i nginx-1.16.1-1.el7.src.rpm
* wget https://www.openssl.org/source/latest.tar.gz
* tar -xvf latest.tar.gz
### собираем зависимости из домашней директории
* cd ~
* yum-builddep rpmbuild/SPECS/nginx.spec
### Правим spec файл и добавляем --with-openssl=/root/openssl-1.1.1d
* nano rpmbuild/SPECS/nginx.spec
##### рехультат успешен
*_ Executing(%clean): /bin/sh -e /var/tmp/rpm-tmp.KxG8Vj _*
*_ + umask 022 _*
*_ + cd /root/rpmbuild/BUILD _*
*_ + cd nginx-1.16.1 _*
*_ + /usr/bin/rm -rf /root/rpmbuild/BUILDROOT/nginx-1.16.1-1.el7.x86_64 _*
*_ + exit 0 _*

### Ставим пакет и проверяем работоспособность nginx
* yum remove nginx
* yum localinstall -y \rpmbuild/RPMS/x86_64/nginx-1.16.1-1.el7.x86_64.rpm
* systemctl start nginx
* systemctl status nginx

##### Создаем свой репозиторий 
#### каталог repo
* mkdir /usr/share/nginx/html/repo
#### копируем рпм
* cp nginx-1.16.1-1.el7.x86_64.rpm /usr/share/nginx/html/repo/
#### создаем репозиторий Percona-Server и инициализируем
* wget http://www.percona.com/downloads/percona-release/redhat/0.1-6/percona-release-0.1-6.noarch.rpm -O /usr/share/nginx/html/repo/percona-release-0.1-6.noarch.rpm
* createrepo /usr/share/nginx/html/repo/
#### создаем и правим конфиг nginx 
* nano  /etc/nginx/conf.d/default.conf
#### смотрим, что все хорошо
* nginx -t
* nginx -s reload
#### добавляем свой репозиторий 
* cat >> /etc/yum.repos.d/1488.repo << EOF
* [1488]
* name=1488repo
* baseurl=http://localhost/repo
* gpgcheck=0
* enabled=1
* EOF

### Смортрим результат
* yum repolist enabled | grep 1488
