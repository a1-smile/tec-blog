# PHPUnit のインストール時につまづいた点について共有します。

## 開発環境
OS: Windows 11 Home
ローカル環境: MAMP (PHP 8.3.1)
Composer: 2.10.2

## エラーの内容
発生したエラーは、以下の2点です。
- コマンドプロンプトで Composer を実行するとき「^」を認識しないエラーが発生する
- PHPunit 12.0.0 にはセキュリティー勧告がされていて、Composer は
 それを理由にインストールを拒否する

私が使用している MAMP の PHP は 8.3.1 です。
この PHP で使用できる PHPUnit のバージョンを調べたところ
 `PHPUnit 12 系`
を使用できることがわかりました。
そこで、PHPUnitのバージョンを指定してインストールを試みました。
```
composer require --dev phpunit/phpunit:^12
```
すると、以下のようなエラーが発生しました。
```
The "12" constraint for "phpunit/phpunit" appears too strict and will likely not match what you want.
```
12 系ではなく 12.0.0 を指定していると認識されました。
```
- Root composer.json requires phpunit/phpunit 12 (exact version match: 12, 12.0, 12.0.0 or 12.0.0.0), found phpunit/phpunit[12.0.0] but these were not loaded, because they are affected by security advisories
```
12.0.0 にはセキュリティー勧告がされていて、Composer はそれを理由にインストールを拒否するようです。
## 解決策
