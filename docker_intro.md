# Docker入門

##　仕組み

![aaaa](imgs/contener_vm.png)

## イメージ作成

- CentOSイメージを検索  
`# docker search centos`
- CentOSイメージをpull(ダウンロード)  
`# docker pull centos`
- イメージを確認  
`# docker images`

## イメージ作成

```
# docker build -t [ユーザ名]/[イメージ名] [DockerFileのパス]
```

## コンテナ起動

defaultという名前でcentosイメージをコンテナ起動する  
`# docker run -i -t -d --name="default" centos /bin/bash`

## コンテナ確認

起動中のコンテナを確認  
`# docker ps `

    CONTAINER ID  IMAGE          COMMAND    CREATED        STATUS            PORTS  NAMES
    55082e783e5f  centos:latest  /bin/bash  2 seconds ago  Up 1 seconds         default

## コンテナに接続

`#docker attach <コンテナID>/<コンテナ名>`  
-  例  
`#docker attach default`

## コンテナをコミット(イメージ作成)

`# docker commit <コンテナID>/<コンテナ名>  <イメージ名(命名)>`  
-  例  
`# docker commit default httpd`

同一のリポジトリに、タグで管理する場合↓  
`# docker commit <コンテナ名/ID> <イメージ名>:<タグ名> `  
-  例  
`# docker commit default centos:httpd`

## コンテナの削除

`# docker rm <コンテナID>/<コンテナ名>`

- 全てのコンテナを削除する  
`# docker rm `docker ps -a -q``

## イメージの削除

`# docker rmi <イメージ名>`

- noneイメージを全て削除する  
`docker rmi $(docker images | awk '/^<none>/ { print $3 }')`

## DokcerFile

### サンプル：Ruby on Rails環境

```
FROM centos:centos6
MAINTAINER xxxxxx
#basics
RUN yum -y install gcc
RUN yum -y install wget zip unzip which tar
RUN yum -y install git zlib-devel perl-ExtUtils-MakeMaker httpd httpd-devel openssl-devel libyaml-devel libxml2-devel libxslt-devel libffi-devel readline-devel pcre-devel iconv-devel sqlite-devel mysql mysql-server mysql-devel postgresql postgresql-server postgresql-devel curl-devel nkf

#RVM,Ruby,Bundler,Rails
RUN gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
RUN curl -L https://get.rvm.io | rvm_tar_command=tar bash -s stable
RUN source /etc/profile.d/rvm.sh
ENV PATH=$PATH:/usr/local/rvm/bin
RUN /bin/bash -l -c "rvm requirements"
RUN /bin/bash -l -c "rvm install 2.1.5 --default"
RUN /bin/bash -l -c "rvm use 2.1.5"
RUN /bin/bash -l -c "gem install bundler --no-ri --no-rdoc"
RUN /bin/bash -l -c "gem install rails --no-ri --no-rdoc"

CMD ["/bin/bash"]
```


## 参考文献

http://qiita.com/gom/items/0bfc1925a7fddfcdfdaf
 
http://qiita.com/mattuso/items/712575dc50513dfdf0a2  

http://codezine.jp/article/detail/7894

http://www.slideshare.net/shin1x1/lt-up-33437883


