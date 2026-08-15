# Windows11 で MAMP を使用している環境で、Composer をインストールしました

## 開発環境
- OS:Windows 11
- ローカル環境: MAMP (PHP 8.3.1)


## あらかじめやっておくと楽なこと
- MAMP の PHP に Path を通しておくと、
Composer のインストールがスムーズに進みます。

後の、開発の際も安全で簡潔です。

とはいえ、
PHP に Path を通すことと、
Composer のセットアップで PHP の実行ファイル を指定することは、別の話です。

Composer のみを使用する場合は、PHP に Path を通さなくても、
Composer のセットアップで PHP の実行ファイルを指定すれば、使用できます。

一方で、
PHP に Path を通しておくと、
Windows が PHP を認識するので、
コマンドプロンプトで、
`php <filename>.php`
として PHP を実行できるようになります。

Windows の動作と
Composer の動作が
整合性を保つためにも、
PHP に Path を通して、
おなじ PHP の実行ファイルを
Windows と
Composer に
指定すると安全です。

たとえば、
Composer が php8.3.1 を利用して
フレームワークをインストール、

コマンドプロンプトで php8.3.1 以外の
PHP を使用して実行、

のような場合
エラーが発生する可能性があります。

単にバージョンをそろえるというよりも、
同じ設定の PHP を使用するようにします。

path の設定方法は、
以下の記事を参考にしてください。
[Windows11 における MAMP の PHP に path を通す方法](https://qiita.com/a1-smile/items/b38783942ec8ef32f8a0)

 path の設定で、
システム環境変数か、
ユーザー環境変数の
どちらに設定するか迷うかもしれません。
個人の PC で、開発環境を構築するなら
最初はユーザー環境変数に設定して、
必要に応じてシステム環境変数に変更するのがおすすめです。

将来、Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する必要がある場合の
拡張性を考えると、システム環境変数に設定する選択肢もあります。

詳しい説明は、以下の記事を参考にしてください。
[システム環境変数とユーザー環境変数どちらか？](https://qiita.com/a1-smile/items/76b5e4b9d5ecdeacb7df)

## インストールの手順
### ダウンロード
1. Composer の公式サイトにアクセスします。
   [https://getcomposer.org/](https://getcomposer.org/)
2. 「Download」ボタンをクリックします。
3. Windows Installer という欄の
    「Composer-Setup.exe」をクリックして、インストーラーをダウンロードします。

### セットアップ
 ダウンロードした「Composer-Setup.exe」をダブルクリックして、
 インストーラーを起動します。
 セットアップが始まります。

1. Select install mode 画面
PC 上の全ユーザーで Composer を使うなら
「Install for all users」
を選択。
PC を自分しか使わない場合も
一般的には「Install for all users」を選択します。

PHP の path を
ユーザー環境変数に設定していても
整合性に問題はありません。

しかし、
「Install for all users」を選んでも
PHP の Path がユーザー環境変数だけにある場合、
Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する場合は、
PHP を見つけられない場合がある点に注意が必要です。

>「Install for all users」を選択する場合は、
>インストールに管理者権限が必要になります。

個人用の開発環境で、
Windows サービス、SYSTEM アカウント、CI、別ユーザーなど、
自分以外のアカウントから Composer を実行する必要がない場合は、
「Install for current user」でも使用可能。

管理者権限がない場合は、「Install for current user」を選択します。

Composer のインストールが他のユーザーに影響を与えないようにするためには、
「Install for current user」を選択。
  
2. Installation Options 画面
   アンインストーラーなしでインストールという意味の
   記述に ✓ を入れずに「Next」をクリックします

3. Setting Check 画面
Composer が使用する php.exe を確認・指定する画面です

ここで必要なのは、実行ファイル php.exe の場所です

たとえば MAMP の PHP が 
`C:\MAMP\bin\php\php8.3.1`
にある場合は、
次のファイルが指定されているかを確認します。
   `C:\MAMP\bin\php\php8.3.1\php.exe`


PHP が自動検出されない場合は、
「Browse」から 
`php.exe` 
を選択します
たとえば MAMP の PHP が 
`C:\MAMP\bin\php\php8.3.1`
にある場合は、
次のファイルを指定します。
   `C:\MAMP\bin\php\php8.3.1\php.exe`


4. Proxy Settings 画面
   インターネットにプロキシサーバーを介して接続している場合は、設定します。

   個人で自宅からインターネットに接続している場合は、そのまま「Next」をクリックします。

5. Ready to Install 画面
   「Install」をクリックして、インストールを開始します

6. Complete Composer Setup 画面で
   「Finish」をクリックして、インストールを完了します


### インストール後の確認
1. コマンドプロンプトを開きます、
すでに開いている場合は再起動して、以下のコマンドを実行します
```
composer --version
```
Composer のバージョンが表示されれば、インストールは成功です