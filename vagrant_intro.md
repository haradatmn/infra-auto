#Vagrant 

## Vagrantコマンド一覧

http://qiita.com/htano/items/0c2ebde44e3fc92b223a

## Vagrantbox

vagrantのイメージは以下から探す
  
http://www.vagrantbox.es/

https://atlas.hashicorp.com/boxes/search?utm_source=vagrantcloud.com&vagrantcloud=1

## Vagrantの基本的な使い方

http://qiita.com/FSMS/items/115378b2fd160e52e4ce


### Vagrantで起動しているVM一覧の確認

以下のプラグインをインストールする  
http://qiita.com/ringo/items/e30761b89fb6c9a1c45d

```
# vagrant global-status -a
```


## Vagrant + Docker

http://qiita.com/hidekuro/items/fc12344d36d996198e96
http://postd.cc/vagrant_with_docker_how_to_set_up_postgres_elasticsearch_and_redis_on_mac_os_x/

## 1. Vagrant&Virtualboxインストール

## 2. Vagrant初期化

```
# mkdir vagrant-sample
# cd vagrant-sample
# vagrant init --minimal centos
```

## 3. VM起動とSSHログイン

```
# vagrant up
・・・・・
# vagrant ssh
```
## 4. vagrant共有フォルダ

ゲストOSの/vagrantがデフォルトで共有される。  
VagrantFileに、以下を指定することもできる。  
```
config.vm.synced_folder "ホスト側Vagrantfileあるディレクトリ", "ゲスト側ディレクトリ"
```


## トラブルシューティング
### Cannot connect to the Docker daemon. Is 'docker -d' running on this host?

以下のパッケージをインストールして再起動する。  
http://exceptiontrail.blogspot.jp/2014/12/docker-140-fails-to-start-due-to-error.html


## 5.Docker設定

VagrantFileに記述して自動化してもよい

### /etc/sysconfig/docker

```
other_args="-H tcp://0.0.0.0:4243 -H unix:// -dns 8.8.8.8"
```

```
sudo sed -i 's, ^other_args="",other_args="-H tcp://0.0.0.0:4243 -H unix:// -dns 8.8.8.8", g' /etc/sysconfig/docker
```

### 環境変数

```
export DOCKER_HOST=tcp://localhost:4243
```

### VagrantFile


```
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos65_x86_64"
  
  config.vm.provider "virtualbox" do |vb|
      vb.name = "vagrant-docker-new"
      vb.customize ["modifyvm", :id, "--memory", 1024]
  end

  config.vm.network "private_network", ip: "192.168.33.11"
  config.vm.network :forwarded_port, guest: 4243, host: 4243


  config.vm.provision :shell, :inline => <<-EOT
    #Before Setting 
    yum --assumeyes install device-mapper-event-libs
  EOT

  config.vm.provision "docker" do |d|
    d.build_image "/vagrant", args: "-t haradatmn/centos-cont"
    d.run "haradatmn/centos-cont", args: "-i -t -v /vagrant:/tmp/shared"
  end

  config.vm.provision :shell, :inline =>  <<-EOT
     #iptables
     /sbin/iptables -F
     /sbin/service iptables stop
     /sbin/chkconfig iptables off

     #Docker
     sudo sed -i 's, ^other_args="",other_args="-H tcp://0.0.0.0:4243 -H unix:// -dns 8.8.8.8", g' /etc/sysconfig/docker
     export DOCKER_HOST=tcp://localhost:4243
  EOT  
```

### DockerFile

```
FROM centos
MAINTAINER xxxxx

RUN yum -y install gcc
# httpd
RUN yum -y install httpd
# Ruby
RUN yum -y install ruby

CMD ["/bin/bash"]
```
	







