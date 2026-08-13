# Windows11 で MAMP を使用している環境で、
Composer をインストールしました

## 開発環境
- Windows 11
- MAMP (PHP 8.3.1)


## あらかじめやっておくと楽なこと
- MAMP の PHP に path を通しておくと、
Composer のインストールがスムーズに進みます。

しかし、
「Settings Check」画面では、php.exe を手動で選択できます。
そのため、インストール自体は PHP のディレクトリが
 Path に未登録でも進められます。

また、
Path を通しておけば、PHP をコマンドプロンプトから
実行できるようにもなります。

path の設定方法は、
以下の記事を参考にしてください。
[Windows11 における MAMP の PHP に path を通す方法](https://qiita.com/a1-smile/items/b38783942ec8ef32f8a0)

 path の設定で、
システム環境変数か、
ユーザー環境変数の
どちらに設定するか迷う場合は、
個人の PC で、開発環境を構築するなら
ユーザー環境変数に設定すればよい場合がほとんどです。

将来、Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する必要がある場合の
拡張性を考えると、システム環境変数に設定する必要があります。

詳しい説明は、以下の記事を参考にしてください。
[Windows11 における 環境変数の設定方法](https://qiita.com/a1-smile/items/76b5e4b9d5ecdeacb7df)

## インストールの手順
1. Composer の公式サイトにアクセスします。
   [https://getcomposer.org/](https://getcomposer.org/)
2. 「Download」ボタンをクリックします。
3. Windows Installer という欄の
    「Composer-Setup.exe」をクリックして、インストーラーをダウンロードします。
4. ダウンロードした「Composer-Setup.exe」をダブルクリックして、インストーラーを起動します。
5. セットアップします、
   セットアップの説明は、「セットアップの説明」の欄を参照してください。
6. インストール後の確認を行います、
   インストール後の確認は、「インストール後の確認」の欄を参照してください。
## セットアップの説明
1. Select install mode 画面
PC 上の全ユーザーで Composer を使うなら
「Install for all users」
を選。
PC を自分しか使わない場合も
一般的には「Install for all users」を選択します。
インストールに管理者権限が必要になります。

Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する場合は、
「Install for all users」を選択すると管理しやすくなります。
ただし、PHP もシステム環境変数の Path に設定するか、
実行する PHP のパスを明示する必要があります。

Install for all users を選んでも
、PHP の Path がユーザー環境変数だけにある場合、
Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する場合は、
PHP を見つけられない場合がある点に注意が必要です。

個人用の開発環境で、
Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する必要がない場合は、
「Install for current user」でも動きます。

管理者権限がない場合は、「Install for current user」を選択します。

Composer のインストールが他のユーザーに影響を与えないようにするためには、
「Install for current user」を選択。


   
2. Installation Options 画面
   アンインストーラーなしでインストールという意味の
   記述に ✓ を入れずに「Next」をクリックします

3. Setting Check 画面
Composer が使用する php.exe を確認・指定する画面です

ここで必要なのは、実行ファイル php.exe の場所です

PHP が自動検出されない場合は、「Browse」から `php.exe` を選択します
たとえば MAMP の PHP が `C:\MAMP\bin\php\php8.3.1` にある場合は、
次のファイルを指定します。
   `C:\MAMP\bin\php\php8.3.1\php.exe`


4. Proxy Settings 画面
   インターネットにプロキシサーバーを介して接続している場合は、設定します。

   個人で自宅からインターネットに接続している場合は、そのまま「Next」をクリックします。

5. Ready to Install 画面
   「Install」をクリックして、インストールを開始します

6. Complete Composer Setup 画面で
   「Finish」をクリックして、インストールを完了します

## インストール後の確認
1. コマンドプロンプトを開く、
すでに開いている場合は再起動して、以下のコマンドを実行します
```
composer --version
```
Composer のバージョンが表示されれば、インストールは成功です




