# .env ファイルの設定と、PHP での使用方法について解説します。
## 概要
DB への接続情報などは、ファイルに直接書かずに、 .env ファイルに記入し、環境変数として読み込むように設定します。

.env ファイルは git 管理はしません。

PHP では環境変数を読み込むライブラリも使えます。

以下で説明していきます。


## ファイルの設定
```text
project-root/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
```

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
`git status` で .env が表示されている場合は、

まず、.gitignore に .env を追加して、
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
>「まだ追跡されていない .env」だけを無視します。
>
>すでにコミット済みの機密情報は、
>
>git rm --cached .env で追跡解除しても
>
>Git の過去履歴には残ります。
>公開リポジトリなどへ push 済みなら、
>DB パスワード等は変更（ローテーション）
>する必要があります。



.env ファイルをまだ作っていない場合は、

プロジェクトのルートディレクトリに
.env ファイルを作成します。

 また、
 サンプルとして、
 `.env.example` ファイルも作成します。

 >.env ファイルには .txt などと拡張子を付けずに作成する必要があります。
 >
>私の場合、
>
>テキストファイルだから txt という拡張子を
>つけなければいけないと勘違いして、
>.env として認識されないエラーに悩まされました。

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

## PHP で環境変数を読み込む方法
`.env` ファイルを読み込むには、
ライブラリの
`vlucas/phpdotenv` を使用します。

Composer をインストール済なら、以下のコマンドでインストールできます。

```bash
composer require vlucas/phpdotenv
```

`vlucas/phpdotenv` の詳しいインストール方法は、
以下のリンクを参照してください。
[vlucas/phpdotenv - Packagist](<https://packagist.org/packages/vlucas/phpdotenv>)

```text
project-root/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
  ├─ .env.php
  ├─ src/
  │   └─ index.php
  |   └─ common/
  |      └─ database.php
  ├─ vendor/
  |   └─ autoload.php
```

上の図のようなディレクトリ構成にして、
環境変数を読み込むファイルを作成。

例えば、
プロジェクトのルートディレクトリに、
`.env.php` ファイルを作成します。
内容は以下のように記述します。

⚠️ インストールしたライブラリのクラスを使用するために、
`/vendor/autoload.php` を読み込む必要があります。

```php
<?php   
// 同じ階層の vendor にある Composer の /vendor/autoload.php を読み込む
require_once __DIR__ . '/vendor/autoload.php';
// インストールしたライブラリのクラスを使用する準備

use Dotenv\Dotenv;
use Dotenv\Exception\InvalidPathException;
use Dotenv\Exception\ValidationException;
use Dotenv\Exception\InvalidFileException;

// .env と .env.php を同じディレクトリ階層に置く場合の設定
$dotenv = Dotenv::createImmutable(__DIR__);

//  .env ファイルに不備があったら、例外がスローされます。
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

> **⚠️注意**
> createImmutable() は、
OS 環境変数や
phpunit.xml で定義された環境変数を上書きしません。
.env の値を使用する場合は、
OS や phpunit.xml で
同名の環境変数を設定しないように注意が必要です。

> load() と safeLoad() の違いについて
>
> - `load()` は、.env ファイルが存在しない場合や形式が正しくない場合に例外をスローします
>
> - `safeLoad()` は、.env ファイルが存在しない場合でも例外をスローせず、存在する場合のみ読み込みます
>
> .env を必須とする場合は `load()` を使用し、
>
> OS 環境変数を使う可能性がある場合は `safeLoad()` を使用します。

> ⚠️ここで、
Dotenv\Dotenv は PHP の名前空間での表記です。
>
>名前空間\クラス名 の形式で記述。
>
>名前空間はフォルダ分けのようなものです。
>
>`use Dotenv\Dotenv;`
と記述した場合は、
その後は、
```php
<?php
use Dotenv\Dotenv;
$dotenv = Dotenv::createImmutable(__DIR__);
$dotenv->load();
```
> のように記述します。
Dotenv という文字列が、
Dotenv\Dotenv と認識されます。

注意事項は以上で、話を.env の使用法にもどしますと、

DB に接続する
database.php などのファイルで、
環境変数を使用して DB に接続することができるように、
```php
<?php
$dbHost = $_ENV['DB_HOST'];
$dbName = $_ENV['DB_NAME'];
$dbUser = $_ENV['DB_USER'];
$dbPassword = $_ENV['DB_PASSWORD'];
$access_info = "mysql:host=$dbHostdbname=$dbName;charset=utf8";

try {
    $pdo = new PDO(
        $access_info,
        $dbUser,
        $dbPassword
    );
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    error_log('Connection failed: ' . $e->getMessage());
    exit;
}
```

などと、記述します。

index.php などのアプリケーションのエントリーポイントで、
`.env.php` を読み込むことで、環境変数を利用できるようになります。
例えば、`src/index.php` では以下のように記述します。
```php
<?php
// 環境変数を読み込む
require_once __DIR__ . '/../.env.php';
// 環境変数を使用して、PDO 接続する
require_once __DIR__ . '/common/database.php';
```

これで、$pdo（PDO オブジェクト）を利用することができるのです。

## テスト環境での `.env` ファイルの利用方法
PHPUnit を使用して、
テスト用の DB を設定して
テストをしたい場合の説明です。

ディレクトリ、ファイル構成が以下のようになる場合の例です。
```text
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
```

処理の流れとしては、テスト実行時に

1. 
`tests/bootstrap.php` で
`tests/.env` を読み込む

2.  
`phpunit.xml` で
`bootstrap` 属性によって
`tests/bootstrap.php` を指定


詳しい設定方法は、以下になります。

.gitignore に `tests/.env` を追加します。
```gitignore
 .env
 tests/.env
 ```

tests/.env を作成し、にテスト用の DB への
接続情報を記述します。
例えば、以下のように記入。
```env
DB_HOST=localhost
DB_NAME=test_database
DB_USER=test_user
DB_PASSWORD=test_password
```

tests/.env.example には、
テスト用の DB 接続情報の例を記述。
パスワードは空欄にしておきます。
例えば、以下のように記載。
```env
DB_HOST=localhost
DB_NAME=test_database
DB_USER=test_user
DB_PASSWORD=
```

phpunit.xml で `bootstrap` 属性に
`tests/bootstrap.php` を指定します。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="tests/bootstrap.php" colors="true">
  <testsuites>
    <testsuite name="App Test Suite">
      <directory>tests</directory>
    </testsuite>
  </testsuites>
</phpunit>
```

tests/bootstrap.php では、
以下のように記述して `tests/.env` を読み込みます。
Dotenv を使う前に Composer の autoloader が必要です。
```php
<?php
// インストールしたライブラリのクラスを使用する準備
//  一階層上のディレクトリに上がって Composer の autoloader を読み込む
require_once __DIR__ . '/../vendor/autoload.php';

use Dotenv\Dotenv;
use Dotenv\Exception\InvalidFileException;
use Dotenv\Exception\InvalidPathException;

// 同じ階層にある tests/.env を読み込む
$dotenv = Dotenv::createImmutable(__DIR__);
try {
    $dotenv->load();
} catch (InvalidPathException $e) {
    //  ファイルが見つからなければ、テスト開始前に停止
    error_log('tests/.env ファイルが見つかりません。環境変数を設定してください。');
    exit('tests/.env ファイルが見つかりません。環境変数を設定してください。');
} catch (InvalidFileException $e) {
    //  ファイルの型式が間違っていれば、テスト開始前に停止
    error_log('tests/.env ファイルの形式が正しくありません。');
    exit('tests/.env ファイルの形式が正しくありません。');
}
```

> phpunit.xml に DB 接続情報を書く方法もあります。
しかし、セキュリティや管理の観点から、
DB 接続情報は `tests/.env` ファイルに記述する方法が
推奨されます。

> **⚠️注意**
> createImmutable() は、
OS 環境変数や
phpunit.xml で定義された環境変数を上書きしません。
tests/.env の値を使用する場合は、
OS や phpunit.xml で
同名の環境変数を設定しないように注意が必要です。

## `.env` ファイルをバックアップするときの注意

.env は暗号化して、バックアップします。

OneDrive などのクラウドストレージに保存する場合はアクセスを制限。

一方で、
.env.example は設定項目を確認できるように git 管理しておきます。

`.env` ファイルには、DB のパスワードや API キーなどの
機密情報が含まれる場合があります。

そのため、バックアップを取る場合も、
通常のソースコードと同じ場所や方法で扱わないように注意します。

- `.env` を Git リポジトリにコミットしない
- メール、チャット、公開フォルダへそのまま添付しない
- クラウドストレージへ保存する場合は、共有設定と閲覧権限を確認する
- バックアップ先のファイルやフォルダを暗号化する
- バックアップを閲覧できる人を必要最小限にする
- 定期的に復元できるか確認する

パスワード管理ツールの安全なメモ機能や、
暗号化されたバックアップ領域を利用すると、
`.env` を比較的安全に保管できます。

また、環境変数の名前や必要な設定項目は
`.env.example` に記載し、
実際のパスワードや API キーは記載しません。

もし `.env` を誤って GitHub などの公開場所へ
push してしまった場合は、
ファイルを削除するだけでは不十分です。

DB パスワードや API キーを無効化・変更し、
必要に応じて Git の履歴からも削除する必要があります。