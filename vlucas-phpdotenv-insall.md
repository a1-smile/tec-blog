# vlucas/phpdotenv インストール

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
#### ファイルの設定
プロジェクト ディレクトリのルートに `.gitignore` ファイルを作成。
.gitignore に
```gitignore
.env   
```
と記述します。

⚠️ 注意: `.env` ファイルには機密情報が含まれるため、
この作業の前に .env ファイルを作ってコミットをしないように注意します。
git の追跡対象になってしまいます。

すでに、
`git status` で .evn が表示されているばあいは、
```cmd
git rm --cached .env
```
```cmd
git commit -m "Remove .env from tracking"
```
として、git の追跡対象から外します。

プロジェクトのルートディレクトリに
 `.env` ファイルと
 `.env.example` ファイルを作成します。

 >.env ファイルには .txt などと拡張子を付けずに作成する必要があります。
私の場合、
テキストファイルだから txt という拡張子を
つけなければいけないと勘違いして、
.env として認識されないエラーに悩まされました。

.env ファイルには、
例えば PDO を使用したデータベース接続情報なら、以下のように記述します。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=your_password
```
サンプルとして、`.env.example` ファイルに
設定の型を書いて
パスワードは空欄にしておきます。
こちらは、git の追跡対象に含めます。
他の環境で、コピーして書き換えることにより、
環境に合わせた `.env` ファイルを作成することができます。
例えば、`.env.example` ファイルの内容は以下のようになります。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=
```
#### php での環境変数の読み込み
`.env` ファイルを読み込むには、`vlucas/phpdotenv` を使用します。
または、
phpunit では bootstrap.php で環境変数を読み込むのが一般的です。


`vlucas/phpdotenv` のインストール方法は、
以下のリンクを参照してください。

[vlucas/phpdotenv - Packagist](https://packagist.org/packages/vlucas/phpdotenv)

環境変数を読み込むファイルを作成します。
例えば、
プロジェクトのルートディレクトリに、
`.env.php` ファイルを作成します。
または、
テスト用なら tests ディレクトリに、
bootstrap.php を作成。

project-root/
  ├─ .env
  ├─ .env.example
  └─ .env.php
  ├─ phpunit.xml
  ├─ src/
  │  └─ index.php
  └─ vendor/
  └─ tests/
     └─ bootstrap.php
`index.php` などで、

環境変数を読み込む関数を定義しておきます。

## 結論
PHP のバージョンが
PHP:^7.2.5 || ^8.0

7.2.5 以上での7.x 系、
または 8.x 系を使用していることが必要。

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