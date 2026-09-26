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

## 設定手順の概要
### .env のファイルの設定
.env ファイルの設定方法と使用法については、

以下の記事を参考にしてください。

[.env のファイルの設定と使用方法](<https://qiita.com/a1-smile/items/eca7ce8aa9cccfb90ed9>)

### PHP のバージョン確認
`vlucas/phpdotenv` をインストールするには、

プロジェクトディレクトリで動く
PHP のバージョンが

`vlucas/phpdotenv` が必要としている PHP の範囲にあるかを確認する必要があります。

以下のリンクから確認できます。
[The PHP Package Repository](https://packagist.org/packages/vlucas/phpdotenv?utm_source=chatgpt.com)

この記事を書いた時点では

Requires

php: ^7.2.5 || ^8.0
ext-pcre: *
graham-campbell/result-type: ^1.2
phpoption/phpoption: ^1.10
symfony/polyfill-ctype: ^1.26
symfony/polyfill-mbstring: ^1.26
symfony/polyfill-php80: ^1.26

つまり、

7.2.5 以上での7.x 系、
または 8.x 系を使用していることが必要とされています。

### Composer を確認する
プロジェクトディレクトリで Composer が動くかを確認。

### インストールコマンド実行
Composer で vlucas/phpdotenv をインストールするコマンドを実行。

### 動作確認
vlucas/phpdotenv で、環境変数を読み込めるかを確認。


## 具体的なインストール手順
### vlucas/phpdotenv を使用するディレクトリまで移動
コマンドプロンプトを使用して、
`cd` コマンドでプロジェクトのルートディレクトリに移動します。
```cmd
cd 「project-nameへのパス」
```

### PHP のバージョン確認
`php -v` コマンドで現在の PHP のバージョンを確認します。
```cmd
php -v
```
ここで、次のように

PHP:^7.2.5 || ^8.0

の範囲の
PHP のバージョンが表示されれば問題ありません。
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

## インストールと動作の確認

プロジェクトのルートディレクトリに `.env` ファイルを設定。

以下のような環境変数を定義します。

```env
DB_HOST=localhost
DB_NAME=student
DB_USER=root
DB_PASSWORD=root
```

プロジェクトのルートディレクトリに

`test-env.php` というファイルを作成。

環境変数を読み込み、出力する設定を以下のように記述します。

```php
//  Composer がインストールしたクラスを
//  使えるようにする記述
require_once __DIR__ . '/vendor/autoload.php';

//  Dotenv\Dotenv は Dotenv という名前空間
//  にある Dotenv というクラスという意味。
$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
try {
    $dotenv->load();
} catch (Dotenv\Exception\InvalidPathException $e) {
exit('.env ファイルが見つかりません');
} catch (Dotenv\Exception\InvalidFileException $e) {
    exit('.env ファイルの形式が正しくありません。');
}

echo $_ENV['DB_HOST']. "\n";
``` 

コマンドプロンプトで、test-env.php を実行します。

まず、どこのディレクトリにいるか確認
```cmd
cd
```
プロジェクトディレクトリにいない場合は移動
```cmd
cd 「project-nameへのパス」
```
test-env.php を実行
```cmd
php test-env.php
```
これで、
```cmd
localhost
```
と表示されれば、環境変数を読み込めています。

## 環境変数を読み込めない場合の確認項目
環境変数が読み込めない場合は、vlucas/phpdotenv がインストールされているか、

や

.env ファイルが正しく設定されているか、などを確認していきます。
### ファイル構成を確認する
ファイル、ディレクトリ構成が、以下のようになっていることを確認します。
```
project-name\
│
├─ .env
├─ composer.json
├─ composer.lock
├─ test-env.php
│
└─ vendor\
    ├─ autoload.php
    └─ vlucas\
        └─ phpdotenv\
```
`project-name` ディレクトリ直下に

`.env` と

`vendor` ディレクトリがある。

`vendor` 直下に

`autoload.php` ファイルと
`vlucas` ディレクトリがある。

`vlucas` ディレクトリ直下に

`phpdotenv` が存在することを確認します。

### autoload.php をコマンドプロンプトで確認

さらに、コマンドプロンプトで
```
cd 「project-name のパス」
```
とプロジェクト ディレクトリに移動して、
```
dir vendor\autoload.php
```
とすると、
```
 ドライブ C のボリューム ラベルは Windows です
 ボリューム シリアル番号は B690-2094 です

 project-name\vendor のディレクトリ

2026/08/10  05:38               748 autoload.php
               1 個のファイル                 748 バイト
               0 個のディレクトリ  98,991,210,496 バイトの空き領域

```
のように表示されるかを確認します。

### phpdotenv が Composer に正しくインストールされているか確認
project-name ディレクトリ直下で
```
composer show vlucas/phpdotenv
```
と打ち込むと、
```
name     : vlucas/phpdotenv
descrip. : Loads environment variables from `.env` to `$_ENV` and `$_SERVER` automagically, and optionally to `getenv()`.
keywords : dotenv, env, environment
versions : * v5.7.0
released : 2026-08-24, 4 weeks ago
type     : library
license  : BSD 3-Clause "New" or "Revised" License (BSD-3-Clause) (OSI approved) https://spdx.org/licenses/BSD-3-Clause.html#licenseText
homepage : 
source   : [git] https://github.com/vlucas/phpdotenv.git 301c07936b16d88628b126b01d082ba153cf4c40
dist     : [zip] https://api.github.com/repos/vlucas/phpdotenv/zipball/301c07936b16d88628b126b01d082ba153cf4c40 301c07936b16d88628b126b01d082ba153cf4c40
path     : project-name\vendor\vlucas\phpdotenv
names    : vlucas/phpdotenv

support
issues : https://github.com/vlucas/phpdotenv/issues
source : https://github.com/vlucas/phpdotenv/tree/v5.7.0

autoload
psr-4
Dotenv\ => src/

requires
ext-pcre *
graham-campbell/result-type ^1.2
php ^7.2.5 || ^8.0
phpoption/phpoption ^1.10
symfony/polyfill-ctype ^1.26
symfony/polyfill-mbstring ^1.26
symfony/polyfill-php80 ^1.26

requires (dev)
bamarni/composer-bin-plugin ^1.8.2
ext-filter *
phpunit/phpunit ^8.5.34 || ^9.6.13 || ^10.4.2

suggests
ext-filter Required to use the boolean validator.
```
などと表示されれば、`vlucas/phpdotenv` は正しくインストールされています。
### composer.json を確認
`composer.json` に
```json
"require": {
        "vlucas/phpdotenv": "^x.x"
    }
```
のように記述されているかを確認します。
`"^x.x"` はインストール時点での最新バージョンを表します。
## test-env.php と vendor の相対的な位置関係を確認
test-env.php での記述で
```php
require_once __DIR__ . '/vendor/autoload.php';
```
にように `autoload.php` を読み込んでいるので、

`require_once __DIR__ .` の後に

実行ファイルがある場所から `vendor/autoload.php` への相対パス

を記入している必要があります。

（上の記述は、`vender`ディレクトリが同じ階層にある場合の書き方です。）

### use の使い方を確認
test-env.php で
```
use Dotenv\Dotenv;

$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
```
と書くのは、間違いです。
```
use Dotenv\Dotenv;
```
と書いたあとは、
`Dotenv` と記述すれば、`Dotenv\Dotenv` と認識されます。

上の書き方ですと、PHP が `Dotenv\Dotenv\Dotenv` をさがしてしまい、
```
C:\dev\ua-check>php test-env.php PHP Fatal error: Uncaught Error: Class "Dotenv\Dotenv\Dotenv" not found in C:\dev\ua-check\test-env.php:6 Stack trace: #0 {main} thrown in C:\dev\ua-check\test-env.php on line 6
```
のように、

`Class "Dotenv\Dotenv\Dotenv" not found`

という意味のエラーが発生します。

### "Dotenv\Dotenv" not found というエラーなら
"Dotenv\Dotenv" not found というエラーメッセージが表示された場合は、

vlucas/phpdotenv が未インストール、

vendor が存在しない

test-env.php での記述で
```php
require_once __DIR__ . '/vendor/autoload.php';
```
の部分で込むパスが違う

という場合があります。

また、

`autoload.php`

が正しく実行されていない可能性も高いです。

その場合は、Composerのオートローダーを再生成してみます。
コマンドプロンプトで、
```cmd
composer dump-autoload
```
を実行します。

### .env ファイルが正しく設定されているかを確認
以下のようなエラーメッセージが表示される場合は、

`.env` を読みこめていません。
```
C:\dev\ua-check>php test-env.php PHP Fatal error: Uncaught Dotenv\Exception\InvalidPathException: Unable to read any of the environment file(s) at [C:\dev\ua-check\.env]. in project-name\vendor\vlucas\phpdotenv\src\Store\FileStore.php:68 Stack trace: #0 project-name\vendor\vlucas\phpdotenv\src\Dotenv.php(222): Dotenv\Store\FileStore->read() #1 project-name\test-env.php(7): Dotenv\Dotenv->load() #2 {main} thrown in project-name\vendor\vlucas\phpdotenv\src\Store\FileStore.php on line 68 project-name>php test-env.php
```
このメッセージで重要な部分は、

`Unable to read any of the environment file(s) at [project-name\.env].`

です。

対策としては、まずコマンドプロンプトで、プロジェクトディレクトリ直下に移動して、以下を実行します。
```cmd
dir /a .env
```
`.env` が表示されない場合は、
- `.env` を設定していない
- `.env` を設定する場所を間違えている
- `.env` に .txt などの拡張子がついている
という可能性があります。

解決策は、

test-env.php と同じ階層に `.env` を作成する。

`.env` に拡張子 `.txt` などがついている場合はとりのぞく。

意図せずに、`.txt` という拡張子が付いてしまうことがあります。

例えば、

Windowsではメモ帳などで、

`.env`

と保存したつもりでも、

`.env.txt`

になっていることがあります。

また、VsCode でテキストファイルとして認識させようとして、 `.txt` と拡張子をつけてしまう可能性もあります。

確認するには、コマンドプロンプトで、プロジェクトディレクトリ直下で
```cmd
dir /a
```
を実行して、
```cmd
.env.txt
```
が表示されれば、拡張子 `.txt` がついていることが原因です。

このようなミスを防ぐために、 Windows では、

エクスプローラーでプロジェクトフォルダーに移動して、

表示

⬇️

表示

⬇️

ファイル名拡張子

で拡張子を表示する設定にしておきます。

## まとめ
`vlucas/phpdotenv` をインストールする手順は

1. プロジェクトのディレクトリへ移動
```cmd
cd 「project-nameへのパス」
```
2. PHPのバージョンを確認
```cmd
php -v
```
3. Composerが正常に動くことを確認
```cmd
composer --version
```
4. インストールコマンドを実行
```cmd
composer require vlucas/phpdotenv
```
5. インストールと動作の確認

ファイル構成を確認して、

実際に環境変数を読み込めるかを確認します。








