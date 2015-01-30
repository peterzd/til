#Setup Rails Environment on Ubuntu 14.04 x32
> 根据经验，最好是选择32位系统，在64位系统上，可能导致装不了RVM

##list of what should be done:
- create non-root user(deploy)
- upload ssh-key
- rvm
- ruby
- rails
- postgresql
- passenger with nginx
- ElasticSearch
- Redis
- upload config/application.yml to VPS

##create non-root user
- `ssh root@xxx.xxx.xxx.xxx`
- `useradd -d /home/deploy -m deploy`
- `passwd deploy`
- `sudo update-alternatives –config editor`: change editor, choose vim
- `visudo`，加上一行： `deploy ALL=(ALL) ALL`
###ssh 设置
- 仍然是root登陆下，`cp /etc/ssh/sshd_config ~`，备份
- `vi /etc/ssh/sshd_config`编辑这个文件，其中是不允许root登陆，允许登陆
```
Port 4321
PermitRootLogin no
...
AllowUsers deploy
```

- `service ssh restart` or `/etc/init.d/ssh restart`，重启server上的SSH服务
- `mkdir ~/.ssh`，创建ssh目录
- 进入本地local machine，`cat ~/.ssh/id_rsa.pub | ssh bill@xxx.xxx.xxx.xxx -p 4321 'cat - >> ~/.ssh/authorized_keys'`, 把本地的ssh_id文件上传到server
- `ssh bill@xxx.xxx.xxx.xxx`
- `sudo chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh/`，到server上改一下ssh目录和刚上传文件的权限
- 之后可以用`deploy`登陆上去，进行操作：

##insatll RVM, ruby, rails, passenger(based on [digit ocean guide](https://www.digitalocean.com/community/tutorials/how-to-install-rails-and-nginx-with-passenger-on-ubuntu))
- `sudo apt-get update`
- `\curl -sSL https://get.rvm.io | bash -s stable` based on [rvm official site](http://rvm.io)
- `source ~/.rvm/scripts/rvm`
- 如果遇到依赖问题，可以这样解决：
	1. `rvm requirements`
	2. `rvmsudo /usr/bin/apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion` 或者`sudo apt-get install build-essential libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion` based on [this guide](http://robmclarty.com/blog/how-to-setup-a-production-server-for-rails-4)
- `rvm install 2.1.2` install ruby, 版本自己选
- `rvm use 1.9.3 --default`: use the version as default
- `rvm rubygems current`: user rubygems
- `gem install rails --no-ri --no-rdoc`: install rails
###passenger
- `gem install passenger --no-ri --no-rdoc`: install passenger
- `rvmsudo passenger-install-nginx-module`: install ngigx module
- 安装一下nginx的start, stop 脚本：based on [this site](http://askubuntu.com/questions/257108/trying-to-start-nginx-on-vps-i-get-nginx-unrecognized-service)

```
# Download nginx startup script
wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh

# Move the script to the init.d directory & make executable
sudo mv init-deb.sh /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx

# Add nginx to the system startup
sudo /usr/sbin/update-rc.d -f nginx defaults
```

- `sudo service nginx start`: start nginx server
- `sudo vim /opt/nginx/conf/nginx.conf`: edit the config file

```
server { 
listen 80; 
server_name example.com; 
passenger_enabled on; 
root /var/www/my_awesome_rails_app/public; 
}
```
- 设置一下允许客户端上传的文件大小: `client_max_body_size 300M;`

- `sudo apt-get install nodejs`：rails 需要nodejs来取代**execjs**

##install postgreSQL(based on [digit ocean guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04))
- `sudo apt-get update`
- `sudo apt-get install postgresql postgresql-contrib`
- `createuser --interactive`: create a new Role. Can set the Role as super-user, so we can create DBs

##ElasticSearch(based on [digit ocean guide](https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-on-an-ubuntu-vps))
> 要注意，在安装的时候就要把安全措施做好，防止发生DDoS攻击

- 安装JDK，选择安装Oracle Java
	- `sudo add-apt-repository ppa:webupd8team/java`
	- `sudo apt-get install oracle-java7-installer`
	- `java -version`: test installed java
- 通过deb包来安装ES:
	- `wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.90.7.deb`
	- `dpkg -i elasticsearch-0.90.7.deb`
	会把文件安装在`/usr/share/elasticsearch`路径下，会把init script安装到`/etc/init.d/elasticsearch`
- 配置文件：在`/etc/elasticsearch`下，有**elasticsearch.yml** and **logging.yml.**，配置**yml**文件
	- `sudo vi /etc/elasticsearch/elasticsearch.yml`
	- uncomment and make it like this: `network.bind_host: localhost`，这样去除public access
	- `script.disable_dynamic: true`:  disable dynamic scripts
	- `sudo service elasticsearch restart`: restart service
	- run `curl -X GET 'http://localhost:9200'` to test install  

##instal Redis(based on [DO guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-redis))
- `sudo apt-get install build-essential tcl8.5`
- `wget http://download.redis.io/releases/redis-2.8.9.tar.gz`: get the Redis
- `tar xzf redis-2.8.9.tar.gz`
- `cd redis-2.8.9`
- `make`
- `make test`
- `sudo make install`
- `cd utils`
- `sudo service redis_6379 start`













































