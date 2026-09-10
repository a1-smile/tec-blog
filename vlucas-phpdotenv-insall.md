# vlucas/phpdotenv インストール

## 開発環境
- OS: Windows 11
- ローカル環境: MAMP
- PHP バージョン: 8.3.1
- Composer バージョン: 2.10.2
## 背景となる知識
PHP では、
環境ごとに異なる設定情報
（データベース接続情報や API キーなど）
を管理する必要があります。
これらの情報をコードに直接記述すると、
セキュリティリスクが高まるだけでなく、
環境ごとに設定を変更するのが手間になります。
そこで、`.env` ファイルを使用して環境変数を管理する方法が推奨されます。
`vlucas/phpdotenv` は、この `.env` ファイルを読み込み、
PHP の環境変数として利用できるようにするライブラリです。

### 設定手順の概要
#### .env のファイルの設定と使用方法
.env ファイルの設定方法と使用法については、

以下の記事を参考にしてください。
[.env のファイルの設定と使用方法](<https://qiita.com/a1-smile/items/eca7ce8aa9cccfb90ed9>)
## 結論
PHP のバージョンが
PHP:^7.2.5 || ^8.0

7.2.5 以上での7.x 系、
または 8.x 系を使用していることが必要。


## インストール
### vlucas/phpdotenv を使用するディレクトリまで移動
`cd` コマンドでプロジェクトのルートディレクトリに移動します。
```cmd
cd C:¥dev¥project-name
```

### PHP のバージョン確認
`php -v` コマンドで現在の PHP のバージョンを確認します。
```cmd
php -v
```
ここで、次のように PHP のバージョンが表示されれば問題ありません。
```
PHP 8.3.1 (cli) (built: Jan 16 2024 11:57:11) (ZTS Visual C++ 2019 x64)
Copyright (c) The PHP Group
Zend Engine v4.3.1, Copyright (c) Zend Technologies
```

### Composer が動くことを確認

`composer --version` コマンドで Composer のバージョンを確認します。
```cmd
composer --version
```
次のように Composer のバージョンが表示されれば問題ありません。
```
Composer version 2.10.2 2026-07-01 11:24:45
PHP version 8.3.1 (C:\MAMP\bin\php\php8.3.1\php.exe)
Run the "diagnose" command to get more detailed diagnostics output.
```
### vlucas/phpdotenv のインストール
Composer を使用してインストールします。

```cmd
composer require vlucas/phpdotenv
```
を実行します。

## インストール確認

プロジェクトのルートディレクトリに `.env` ファイルを作成し、環境変数を定義します。

```env
APP_NAME=MyApp
APP_ENV=local
APP_KEY=base64:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

PHP ファイルで `Dotenv` を読み込みます。

```php
require 'vendor/autoload.php';

$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
$dotenv->load();

echo $_ENV['APP_NAME'];
```   