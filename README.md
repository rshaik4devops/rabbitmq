# RabbitMQ Installation

RabbitMQ is a messaging Queue which is used by some components of the applications.

## Manual Installation
1. yum install socat logrotate -y

## primary RabbitMQ signing key
rpm --import https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
## modern Erlang repository
rpm --import 'https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key'
## RabbitMQ server repository
rpm --import 'https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key'



2. Setup YUM repositories for RabbitMQ.

```
create a repo file with the name rabbitmq.repo under /etc/yum.repos.d/
# vi /etc/yum.repos.d/rabbitmq.repo

add the below repo to the file we have created above.

##
## Zero dependency Erlang RPM
##

[modern-erlang]
name=modern-erlang-el9
# uses a Cloudsmith mirror @ yum.novemberain.com.
# Unlike Cloudsmith, it does not have any traffic quotas
baseurl=https://yum1.novemberain.com/erlang/el/9/$basearch
        https://yum2.novemberain.com/erlang/el/9/$basearch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-noarch]
name=modern-erlang-el9-noarch
# uses a Cloudsmith mirror @ yum.novemberain.com.
# Unlike Cloudsmith, it does not have any traffic quotas
baseurl=https://yum1.novemberain.com/erlang/el/9/noarch
        https://yum2.novemberain.com/erlang/el/9/noarch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/noarch
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-source]
name=modern-erlang-el9-source
# uses a Cloudsmith mirror @ yum.novemberain.com.
# Unlike Cloudsmith, it does not have any traffic quotas
baseurl=https://yum1.novemberain.com/erlang/el/9/SRPMS
        https://yum2.novemberain.com/erlang/el/9/SRPMS
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/rpm/el/9/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1


##
## RabbitMQ Server
##

[rabbitmq-el9]
name=rabbitmq-el9
baseurl=https://yum2.novemberain.com/rabbitmq/el/9/$basearch
        https://yum1.novemberain.com/rabbitmq/el/9/$basearch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/9/$basearch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el9-noarch]
name=rabbitmq-el9-noarch
baseurl=https://yum2.novemberain.com/rabbitmq/el/9/noarch
        https://yum1.novemberain.com/rabbitmq/el/9/noarch
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/9/noarch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el9-source]
name=rabbitmq-el9-source
baseurl=https://yum2.novemberain.com/rabbitmq/el/9/SRPMS
        https://yum1.novemberain.com/rabbitmq/el/9/SRPMS
        https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/rpm/el/9/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key
gpgcheck=0
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md
```

3. Install RabbitMQ 

```
# yum update -y
# yum install -y erlang rabbitmq-server
```

4. Start RabbitMQ 

```
# systemctl enable rabbitmq-server 
# systemctl start rabbitmq-server 
```

RabbitMQ comes with a default username / password as `guest`/`guest`. But this user cannot be used to connect. Hence we need to create one user for the application.

5. Create application user

```
# rabbitmqctl add_user roboshop roboshop123
# rabbitmqctl set_user_tags roboshop administrator
# rabbitmqctl set_permissions -p / roboshop ".*" ".*" ".*"
```

Ref link : https://www.rabbitmq.com/rabbitmqctl.8.html#User_Management

