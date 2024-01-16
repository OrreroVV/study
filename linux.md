# 配置基本环境

## 安装基础包

```c
sudo apt-get install build-essential
```



## 安装$sudo$

```c
apt-get install sudo
```

如果返回$No \ such \ file\  or \ directory$，则执行：

```c
apt-get update
```

再执行

```c
apt-get install sudo
```

升级$apt-get$

```python
sudo apt-get update 	#更新源
sudo apt-get upgrade	#更新安装包
```

## 安装$ssh$

```python
sudo apt install -y openssh-server	#安装ssh服务器
sudo apt install -y openssh-client	# 安装ssh客户机
```

## 下载$vim$

```python
sudo apt-get remove vim-common	# 卸载vim-common版本
sudo apt-get install vim		# 下载最新版本的vim
```

## 配置$ssh$远程登录

```python
sudo apt-get install openssh-server	#安装SSH
sudo apt-get install openssh-client
sudo service ssh restart
dpkg -l | grep ssh	#查看SSH进程
ps -e | grep ssh	#确认SSH是否启动
```



## 安装$node$

### 安装

```c++
sudo apt-get install nodejs
sudo apt-get install npm
```

### 升级

```python
sudo npm install -g n
sudo n stable # latest #(升级node.js到最新版) stable #（升级node.js到最新稳定版）
node -v
npm -v
```

## 安装$yarn$

```c++
npm install -g yarn
yarn --version
```



# $linux$常用指令







## 连接

$ssh$默认连接到$22$端口

```c
ssh user@IP
```



$ssh$到指定端口

```c++
ssh -p xxx user@IP
```







## 创建文件

```c
touch xxx.xx
```

## 显示内容

```c
cat xxx
```

## 创建用户

```c
adduser hzh
```

```c
sudo usermod -aG sudo hzh
```

$su -hzh$切换到$hzh$用户中



# $git$



## 添加公钥

- 生成公钥

  ```c
  ssh-keygen
  ```

- 复制公钥

  ```c
  cd .ssh/
  ```

  ```c
  cat id_rsa.pub
  ```

- 绑定到$git$上



## 上传

- **查看该目录下状态**

  ```c
  git status
  ```

- **添加该目录下变化**

  ```c
  git add .
  ```

- **$commit$**

  ```c
  git commit -m "xxxxxx"
  ```

  



## 从$git$仓库更新

```c
git pull
```

放弃本地修改，拉取远程仓代码，原修改代码会丢失并不可恢复（与远程仓一致）

```c
git reset --hard
git pull
```









## 更新$git-ignore$

- **清除缓存**

  ```c
  git rm -r --cached .
  ```

- **修改$.gitignore$**

  ```c
  vim .gitignore
  
  ```

- **更新**

  ```c++
  git add .
  git commit -m "clear cached"
  
  ```

  









# $nginx$



## 安装依赖

```python
sudo apt-get install gcc
sudo apt-get install zlib1g-dev
sudo apt-get install libpcre3 libpcre3-dev
sudo apt-get install openssl libssl-dev
```





## 安装

```python
cd /usr/local/
wget http://nginx.org/download/nginx-1.19.5.tar.gz                        //下载nginx
tar -zxvf nginx-1.19.5.tar.gz                                             //解压ng
mv nginx-1.19.5 nginx
cd nginx
wget https://github.com/winshining/nginx-http-flv-module/archive/master.zip           
unzip nginx-http-flv-module-master.zip
./configure --prefix=/usr/local/nginx --add-module=./nginx-http-flv-module-master --with-http_ssl_module    //编译安装nginx，并指定上面下载的模块路径
make && make install
```

