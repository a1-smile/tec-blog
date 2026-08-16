# Windows11 で MAMP を使用している環境で、Composer をインストールしました

## 開発環境
- OS:Windows 11
- ローカル環境: MAMP (PHP 8.3.1)


## 予備知識
開発環境で参照する PHP は、
MAMP は MAMP 自体の設定（ または MAMP のデフォルト ）
Windows は環境変数で設定した Path  
Composer はセットアップでの設定
できまります。
それぞれ
同じ MAMP の PHP を使うようにしておくと、
CLI と Composer の動作差異を減らせます。

Composer だけを考えれば
セットアップで PHP の実行ファイルを指定すれば、
動きますが、

一方で、
Composer が php8.3.1 を利用して
フレームワークをインストール、

コマンドプロンプトで php8.3.1 以外の
PHP を使用して実行、
のような場合
エラーが発生する可能性もあるわけです。

単にバージョンをそろえるというよりも、
同じ設定の PHP を使用するようにします。

そのために、PHP の Path を
MAMP で使用している PHP フォルダを参照するように
環境変数で設定しておきます。

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

将来、SYSTEM アカウントなどから
Composer を実行する必要がある場合の
拡張性を考えると、システム環境変数に設定する選択肢もあります。

詳しい説明は、以下の記事を参考にしてください。
[システム環境変数とユーザー環境変数どちらか？](https://qiita.com/a1-smile/items/76b5e4b9d5ecdeacb7df)

## インストールの手順
### ダウンロード
1. Composer の公式サイトにアクセスします。
   [https://getcomposer.org/](https://getcomposer.org/)
2. 「Download」ボタンをクリックします
3. Windows Installer という欄の
    「Composer-Setup.exe」をクリックして、インストーラーをダウンロードします

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

>⚠️
各ユーザーが CLI で実行する php の参照先は、
それぞれの PATH 設定に依存します

>⚠️
「Install for all users」を選択する場合は、
インストールに管理者権限が必要になります。

個人用の開発環境で、
SYSTEM アカウントなど、
自分以外のアカウントから Composer を実行する必要がない場合は、
「Install for current user」でも使用可能。

管理者権限がない場合は、「Install for current user」を選択します。

Composer のインストールが他のユーザーに影響を与えないようにするためには、
「Install for current user」を選択。
  
2. Installation Options 画面
   この画面は、
   その時のインストーラーによって
   表示される内容が異なる場合があります。

   特別な理由がなければ既定値のまま進めます。

3. Setting Check 画面
Composer が使用する php.exe を確認・指定する画面です

ここで必要なのは、実行ファイル php.exe の場所です

▪️PHP が自動検出される場合
たとえば MAMP の PHP が 
`C:\MAMP\bin\php\php8.3.1`
にある場合は、
次のファイルが指定されているかを確認します。
   `C:\MAMP\bin\php\php8.3.1\php.exe`

▪️PHP が自動検出されない場合
「Browse」から 
`php.exe` 
を選択します
たとえば MAMP の PHP が 
`C:\MAMP\bin\php\php8.3.1`
にある場合は、
次のファイルを指定します。
   `C:\MAMP\bin\php\php8.3.1\php.exe`

4. Proxy Settings 画面
   インターネットにプロキシサーバーを介して接続している場合は、設定します

   個人で自宅からインターネットに接続している場合は、そのまま「Next」をクリックします。

5. Ready to Install 画面
   「Install」をクリックして、インストールを開始します

6. Complete Composer Setup 画面で
   「Finish」をクリックして、インストールを完了します


### インストール後の確認
コマンドプロンプトを開きます。
すでに開いている場合は再起動して、以下のコマンドを実行します。

```
where php
```
`C:\MAMP\bin\php\php8.3.1\php.exe` 
 というように、
 意図した PHP が表示されることを確認します。

```
php --version
```
`PHP 8.3.1 (cli) (built: Jan 16 2024 11:57:11) (ZTS Visual C++ 2019 x64)
Copyright (c) The PHP Group
Zend Engine v4.3.1, Copyright (c) Zend Technologies`
などと、 PHP のバージョンが正しいことを確認します。

```
composer --version
```
`Composer version 2.10.2 2026-07-01 11:24:45
PHP version 8.3.1 (C:\MAMP\bin\php\php8.3.1\php.exe)
Run the "diagnose" command to get more detailed diagnostics output.`
という様に Composer のバージョンが表示されることを確認します。
```
composer diagnose
```
`Checking pubkeys: 
Tags Public Key Fingerprint: 57815BA2 7E54DC31 7ECC7CC5 573090D0  87719BA6 8F3BB723 4E5D42D0 84A14642
Dev Public Key Fingerprint: 4AC45767 E5EC2265 2F0C1167 CBBB8A2B  0C708369 153E328C AD90147D AFE50952
OK
Checking Composer version: OK
Composer version: 2.10.2
Checking Composer and its dependencies for vulnerabilities: OK
PHP version: 8.3.1
PHP binary path: C:\MAMP\bin\php\php8.3.1\php.exe
OpenSSL version: OpenSSL 3.0.12 24 Oct 2023
curl version: 8.4.0 libz 1.2.11 brotli missing zstd missing ssl OpenSSL/3.0.12 HTTP 1.0, 1.1, 2
zip: extension present, unzip not available, 7-Zip present (7z)
Checking platform settings: OK
Checking git settings: OK git version 2.49.0
Checking http connectivity to packagist: OK
Checking https connectivity to packagist: OK
Checking github.com rate limit: OK
Checking disk free space: OK`
などと、 Composer の診断結果を確認します。

## まとめ
MAMP の PHP と CLI／Composer の PHP を揃えておくことが重要です。
そのために、PHP の参照先を確認すれば、
トラブルを未然に防ぐことへつながります。

最後まで、読んでいただき、ありがとうございます。