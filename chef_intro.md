#Chef入門

## Chef-Solo
ローカル環境からChef実行することで、リモート操作できる。  
※以下、ローカル環境での実行。

### 事前：Knife-Soloインストール
`# sudo gem install knife-solo`  
`# sudo gem install berkshelf`

### 1. chefリポジトリ作成
任意のファイルで以下を実行するとリポジトリが作成される  
※vagrantを使用する場合は、VagrantFileがあるフォルダで実行が適切  
`#knife solo init .`

### 2. chefをリモートからインストール
サーバにリモートからchef-soloをインストールする  
`#knife solo bootstrap <ホスト名>/<IPアドレス>`

### 3. CookBook作成
/site-cookbooks配下に作成するのがご作法。  
`# knife cookbook create <ブック名> -o site-cookbooks`

### 4. レシピ作成

site-cookbooks/<クックブック名>/recipes/default.rb
を編集する
 
例：dstatをインストール↓  

     package "dstat" do
       action: install
     end

### 5. CookBook実行

`# knife solo cook <ホスト名>/<IPアドレス>`

## kitchen-test + serverspec

レシピのテストには、Kitchen-Testとserverspecを使う手順。
apacheインストールのテストを参考に説明する。

### 事前：CookBook&レシピ作成
任意の場所でクックブックを作成する。  

`# knife cookbook create httpd -o .`

その後、レシピも作成する。  
apacheをインストールして、起動するレシピ↓

__recipes/default.rb__

    package "httpd" do
      action :install
    end

    service "httpd" do
      action [ :enable, :start ]
    end
    
    
### 事前：Test Kitchenをインストール
GemからTestKitchenをインストールする。
以下のGemfileをクックブックのROOTに作成する。

__Gemfile__

    source 'https://rubygems.org'
    gem 'test-kitchen', '~> 1.2.0'
    gem 'kitchen-vagrant', :group => :integration
    gem 'berkshelf'

Gemfileを作成した、以下のコマンドを実行してインストールする

`# bundle install `

### 事前：Test Kitchenの初期化
クックブックのROOTフォルダで以下のコマンドを実行する。  
いくつかのファイル、フォルダが作成される。(.kitchen.yml, test/... などなど)

`# bundle exec kitchen init`

.kitchen.yumlには実行するテスト環境の情報があるため、任意で編集する。  

__.kitchen.yuml__

    driver:
      name: vagrant

    provisioner:
      name: chef_solo
    
    platforms:
      - name: centos-6.5

    suites:
      - name: default
        run_list:
          - recipe[kitchen-test::default]
        attributes:

### 1. テストコード作成(serverspec)
serverspecでテストコードを作成する。  
以下のフォルダに「*_spec.rb」で作成する。（フォルダが無いため、作成すること）

_test/integration/default/serverspec/localhost/default_spec.rb_

    require 'serverspec'
    set :backend, :exec
    set :path,'/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin'

    describe package('httpd') do
      it{ should be_installed }
    end

    describe service('httpd') do
      it{ should be_enabled }
      it{ should be_running }
    end

##### serverspec v1→v2

テストコードの記述方法が違うため、注意↓  
http://serverspec.org/changes-of-v2.html

### 2. テスト実行

クックブックのROOTフォルダで以下のコマンドを実行する。  

`# bundle exec kitchen test`

### Test Kitchenコマンド

kitchen testは以下の順でコマンド実行するのと同じ。

- create
- setup
- converge
- verify
- destory 

## 参考文献

- Chef実践入門 - コードによるインフラ構成の自動化

- 今更聞けない人の為の Chef 再入門  
http://blog.schoolwith.me/chef-re-introduction/

- chef soloの簡単な使い方、設定方法一覧  
http://hivecolor.com/id/126

- serverspecでテストを書いた  
http://watashideath.tumblr.com/post/70890071015/serverspec

- 【AWS】JenkinsとserverspecでChefのテストを自動化する  
http://dev.classmethod.jp/cloud/aws/aws-jenkins-run-ec2-and-chef-cooking/

## 参考：サーバ内作業
以下は入門のための参考程度  
※knife-soloを利用して、リモート環境（ローカルPC）からChef操作するのが一般的。  
※以下、サーバ環境での操作

### Chef-Soloインストール

ゲストOS上で以下を実施する  
`curl -L https://www.opscode.com/chef/install.sh | sudo bash`

バージョン確認  
`# chef-solo -v`  
`Chef: 12.0.3`

###CookBook作成

`# knife cookbook create <クックブック名> -o <保存先>`  

- 例  
`# knife cookbook create hello -o /var/chef/cookbooks`

### CookBook実行

`# chef-solo -o hello`
