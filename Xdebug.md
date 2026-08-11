# 【 Xdebug を導入】Windows MAMP  VSCode の環境に （備忘録）

## はじめに
Windows MAMP VSCode の環境に「Xdebug」を導入しました。
私が行った手順を共有。

なお、MAMP には Xdebug の拡張機能が梱包されています。

Xdebug の ダウンロードは不要。

"C:\MAMP\bin\php\php8.3.1\ext\php_xdebug.dll"
というようなファイルが MAMP に最初からあり、
これを有効化します。

この記事が、
同じ環境にデバッガーを導入する方の参考になれば幸いです。

## 開発環境
- OS: Windows 11 
- ローカル環境: MAMP (PHP バージョン: 8.3.1)
- エディタ: Visual Studio Code

## 思っていたのとは違ったこと
私はプリントデバッグのように、
ブラウザ表示を確認しながら、
デバッグをすることをイメージしていました。

しかし、
プログラムが
ブレークポイントで止まった時点での
ブラウザ表示の確認は、
通常できません。
php が止まるため、特別な設定をしない限り
html ファイルがブラウザに送信されないからです。

ブレークポイントでの変数の状態を確認することや、
ブレークポイントからのステップ実行に
デバッガーを使用します。

---
## 0. 手順の流れ
1. php version 確認
1. Xdebug が php で有効化されているか確認
    （ 私の場合は有効化されていませんでした。）
1. php.ini に Xdebug 有効化の記述
1. Xdebug が php で有効化されたかを確認
    （ Xdebug の version とポート番号も確認 ）
1. VSCode で拡張機能をインストール
1. launch.json を作成、ポート番号を確認
1. 動作確認

## 1. php の version を確認する
開発中のフォルダー直下に
info.php 
という名前のファイルを作り、
```
<?php
phpinfo();
```
と記述して、ブラウザーで表示。
最上段に書いてある、php の version を確認します。
私の場合 php 8.3.1 でした。
この情報は php.ini ファイルを探すときなどに使用。
web ページはまだ閉じずに次の確認に進みます。
## 2. Xdebug が有効化されているか確認
php version を確認したのと同じ Web ページで
Ctrl+F のショートカットで検索窓を表示して、
Xdebug と入力して検索。
Xdebug の欄があれば、有効化されています。
私の場合は、見つかりませんでした。
見つからない場合は次の Xdebug 有効化の記述をおこないます。
## 3. php.ini に Xdebug 有効化の記述
Xdebug が有効化されていない場合は、
C:\MAMP\conf\phpx.x.x\php.ini
というファイルを開きます。
( x.x.x の部分はphp の version です。私の場合は 8.3.1 )

以下の記述があるかを
検索します。
```
;zend_extension="php_xdebug.dll"
```
この記述がある場合は、行頭の「;」を削除すれば
Xdebug が有効化されます、

しかし、私の場合はこの記述がありませんでした。


以下の記述がありましたが、
この記述では有効化されないと、
Gemini からアドバイスされました。

❌❌ダメな記述例
```
;extension=php_xdebug.dll
```
zend_ という部分がないのと、
php_xdebug.dllが「"」で囲まれていない
ところがちがいます。
zend_extension="php_xdebug.dll" の記述が
php.ini にない場合は、ファイル末尾に以下のように
追記します。
```
[xdebug]
zend_extension="php_xdebug.dll"
xdebug.mode=debug
xdebug.start_with_request=yes
```
ここで、
```
xdebug.client_port=9003
```
と、
Xdebug のポート番号を明示的に
書き添えてもよいのですが、
Xdebug の version 3 以降は
php.ini にポート番号を明記していない場合は
デフォルトで 9003 を使うことになっているので
私の場合は記入しませんでした。

php.ini の記入が終わったら、次の
Xdebug が php で有効化されたかを確認、
さらに、
Xdebug の version と、ポート番号の確認にすすみます。
## 4. Xdebug が php で有効化されたかを確認
MAMP のサーバーを再起動して、
info.php をもう一度表示。
「Xdebug」で検索して、
Xdebug の欄があることを確認。
その欄に記載してある 
xdebug の version が 3 以降であることを確認。
( 私の場合は 3.2.0 でした。)
また、
xdebug.client_port という項目に 9003 と
書いてあることを確認します。

このような状態なら、
php.ini にポート番号を明記していない場合でも、
デフォルトで Xdebug は 9003 のポートを使用します。
そうでなければ、
C:\MAMP\conf\phpx.x.x\php.ini
のファイルの
```
[xdebug]
zend_extension="php_xdebug.dll"
xdebug.mode=debug
xdebug.start_with_request=yes
```
の部分に

```
xdebug.client_port=9003
```
を追記します。

もう一つ、
xdebug.mode という欄が「デバッグ」と表示されているかも確認しておきます。

## 5. VSCode で拡張機能をインストール
VSCode を起動して、
Ctrl+Shift+X のショートカットで、
拡張機能の検索欄を表示して、
PHP Debug と入力
同じ名前の拡張機能が複数表示されますが、
提供元の部分に
「 ✓Xdebug 」と書いてある拡張機能を
インストールします。

## 6. launch.json を作成、ポート番号を確認
PHP Debug の初回の設定を行います。
VSCode で
デバッグを行いたいプロジェクトフォルダーを開いて、
Ctrl+Shift+D のショートカットを入力すると、
「launch.json ファイルを作成」
という表示がされるので、
クリックします。
launch.json ファイルが表示されますので、
```
"port":9003
```
という記述があることを確認します、
あれば設定完了です。

## 7. 動作確認
1. VSCode で、デバッグを行うファイルを開きます
1. Ctrl+Shift+D のショートカットで Xdebug の画面を表示
1. F5 を押すと Xdebug が起動して、画面下の帯状の部分がオレンジ色にかわります
1. 処理を止めたい行番号の左側をクリックすると、赤丸が表示されます
1. Ctrl+J のショートカットでVSCode下部に黒い画面を表示します
1. 「ターミナル」をクリックします
1. 右のほうに powershell と表示されていればそのまま、
そうでなければプルダウンから powershell を選択します
1. 黒い画面に、以下のコマンドを打ち込みます
```powershell
Invoke-WebRequest "localhost/（フォルダー名）/（ファイル名）"

```
例えば、
ファイルのパスが
C:\MAMP\htdocs\project\index.php
の場合は、
```powershell
Invoke-WebRequest "localhost/project/index.php"

```
というように打ち込みます。
1. 赤丸がついた行のところが黄色く表示されて、
左側に変数の状態が表示されれば、導入成功です

## まとめ
Windows MAMP VSCode の環境に「Xdebug」を導入する手順をまとめました。
効率的にデバッグを行うために、Xdebug の導入は非常に有用です。

最後まで読んでいただき、ありがとうございました。