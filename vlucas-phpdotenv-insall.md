# vlucas/phpdotenv インストール

## 結論
PHP のバージョンが
PHP:^7.2.5 || ^8.0
7.2.5 以上での7.x 系、または 8.x 系を使用していることを確認してください。

## インストールする理由
コードに直接、DB接続情報などを記述していると 
セキュリティリスクが高まる、
また、
環境ごとに設定を変更するのが手間がかかる、
と指摘を受けることが多いです。

## 開発環境
- OS: Windows 11
- ローカル環境: MAMP
- PHP バージョン: 8.3.1
- Composer バージョン: 2.10.2

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

`composer -v` コマンドで Composer のバージョンを確認します。
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


## 使用方法
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