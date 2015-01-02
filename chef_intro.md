#Chef入門

## リモート作業

### Knife-Soloインストール
`# sudo gem kenif-solo`  
`# sudo gem berkshelf`

### chefリポジトリ作成
任意のファイルで以下を実行するとリポジトリが作成される  
※vagrantを使用する場合は、VagrantFileがあるフォルダで実行が適切  
`#knife solo init .`

### chefをリモートからインストール
サーバにリモートからchef-soloをインストールする  
`#knife solo bootstrap <ホスト名>/<IPアドレス>`

### CookBook作成
/site-cookbooks配下に作成するのがご作法。  
`# knife cookbook create <ブック名> -o site-cookbooks`

### レシピ作成

site-cookbooks/<クックブック名>/recipes/default.rb
を編集する

     package "dstat" do
       action: install
     end

### CookBook実行

`# chef solo cook <ホスト名>/<IPアドレス>`

## 参考文献

- Chef実践入門 - コードによるインフラ構成の自動化


## 参考：サーバ内作業
以下は入門のための参考程度  
※knife-soloを利用して、リモート環境（ローカルPC）からChef操作するのが一般的。

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
