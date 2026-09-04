# .env ファイルの設定と使用方法

## ファイルの設定

project-root/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example

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
`git status` で .env が表示されているばあいは、

まづ、.gitignore に .env を追加して、
以下のコマンドを実行します。
```cmd
git rm --cached .env
```
そして、
```cmd
git commit -m "Remove .env from tracking"
```
として、git の追跡対象から外します。

>.gitignore は
「まだ追跡されていない .env」だけを無視します。
すでにコミット済みの機密情報は、
git rm --cached .env で追跡解除しても
Git の過去履歴には残ります。
公開リポジトリなどへ push 済みなら、
DB パスワード等は変更（ローテーション）
する必要があります。


プロジェクトのルートディレクトリに
.env ファイルが存在しない場合は、
 `.env` ファイルを作成。
 サンプルとして、
 `.env.example` ファイルも作成します。

 >.env ファイルには .txt などと拡張子を付けずに作成する必要があります。
私の場合、
テキストファイルだから txt という拡張子を
つけなければいけないと勘違いして、
.env として認識されないエラーに悩まされました。

.env ファイルの内容は、
例えば PDO を使用したデータベース接続情報なら、以下のように記述します。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=your_password
```
サンプルとしての、
`.env.example` ファイルには
必要な環境変数の名前と記入例を記述し、
パスワードは空欄にしておきます。
こちらは、git の追跡対象に含めます。
他の環境で、コピーして書き換えることにより、
環境に合わせた `.env` ファイルを作成することができます。

`.env.example` ファイルの内容は以下のようになります。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=
```

## PHP での環境変数の読み込み方法
`.env` ファイルを読み込むには、
ライブラリの
`vlucas/phpdotenv` を使用します。

`vlucas/phpdotenv` のインストール方法は、
以下のリンクを参照してください。

[vlucas/phpdotenv - Packagist](https://packagist.org/packages/vlucas/phpdotenv)

project-root/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
  └─ .env.php
  └─ src/
     └─ index.php
     └─ common/
        └─ database.php

環境変数を読み込むファイルを作成。
例えば、
プロジェクトのルートディレクトリに、
`.env.php` ファイルを作成します。
内容は以下のように記述します。
```php
<?php
use Dotenv\Dotenv;
use Dotenv\Exception\InvalidPathException;
use Dotenv\Exception\ValidationException;
use Dotenv\Exception\InvalidFileException;

$dotenv = Dotenv::createImmutable(__DIR__);

try {
    $dotenv->load();
} catch (InvalidPathException $e) {
    exit('.env ファイルが見つかりません。環境変数を設定してください。');
} catch (InvalidFileException $e) {
    exit('.env ファイルの形式が正しくありません。');
}

//  必須の設定値を確認する場合は、以下のように記述します。
try {
$dotenv->required([
    'DB_HOST',
    'DB_NAME',
    'DB_USER',
    'DB_PASSWORD',
    ])->notEmpty();
} catch (ValidationException $e) {
    error_log($e->getMessage());
    exit('必須の環境変数が設定されていません: ');
}
```

> ここで、
`use Dotenv\Dotenv;`
と記述した場合は、
その後は、
```php
<?php
use Dotenv\Dotenv;
$dotenv = Dotenv::createImmutable(__DIR__);
$dotenv->load();
```
のように記述します。
Dotenv という文字列が、
Dotenv/Dotenv と認識されます。

また、
DB に接続する
database.php などのファイルで、
環境変数を使用して DB に接続することができるように、
```php
<?php
$dbHost = $_ENV['DB_HOST'];
$dbName = $_ENV['DB_NAME'];
$dbUser = $_ENV['DB_USER'];
$dbPassword = $_ENV['DB_PASSWORD'];
$access_info = "mysql:host=$dbHost;dbname=$dbName;charset=utf8";
try {
    $pdo = new PDO(
        $access_info,
        $dbUser,
        $dbPassword
    );
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    echo 'Connection failed: ' . $e->getMessage();
    exit;
}
```

などと、記述します。

index.php などのアプリケーションのエントリーポイントで、
`.env.php` を読み込むことで、環境変数を利用できるようになります。
例えば、`src/index.php` では以下のように記述します。
```php
<?php
require_once __DIR__ . '/../.env.php';
require_once __DIR__ . '/common/database.php';
```

これで、$pdo（PDO オブジェクト）を利用することができます。

## テスト環境での `.env` ファイルの利用方法
PHPUnit を使用して、
テスト用の DB を設定して
テストをしたい場合の説明です。

ディレクトリ構成は以下のようになります。

project-root/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
  └─ .env.php
  └─ src/
  |   └─ index.php
  |   └─ common/
  |      └─ database.php
  |
  |- phpunit.xml
  |
  |- vendor/
  |
  └─ tests/
     └─ bootstrap.php
     └─ .env
     └─ .env.example


.gitignore に以下を追加します。
```gitignore
 .env
 tests/.env
 ```

tests/.env にテスト用の環境変数を記述します。例えば、以下のように記述します。
```env
DB_HOST=localhost
DB_NAME=test_database
DB_USER=test_user
DB_PASSWORD=test_password
```

tests/.env.example には、テスト用の環境変数の例を記述します。例えば、以下のように記述します。
```env
DB_HOST=localhost
DB_NAME=test_database
DB_USER=test_user
DB_PASSWORD=
```

