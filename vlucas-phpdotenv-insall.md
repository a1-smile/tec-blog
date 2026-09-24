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
プロジェクトディレクトリで動く
PHP のバージョンが
PHP:^7.2.5 || ^8.0

つまり、

7.2.5 以上での7.x 系、
または 8.x 系を使用していることが必要。

### Composer を確認する
プロジェクトディレクトリで Composer が動くかを確認。

### インストールコマンド実行
Composer で vlucas/phpdotenv をインストールするコマンドを実行。

### 動作確認
vlucas/phpdotenv で、環境変数を読み込めるかを確認。


## インストール
### vlucas/phpdotenv を使用するディレクトリまで移動
コマンドプロンプトを使用して、
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

プロジェクトのルートディレクトリに設定した `.env` ファイルに、以下のような環境変数を定義します。

```env
DB_HOST=localhost
DB_NAME=student
DB_USER=root
DB_PASSWORD=root
```

プロジェクトのルートディレクトリに

`test-dotenv.php` というファイルを作成。

環境変数を読み込み、出力する設定を以下のように記述します。

```php
//  Composer がインストールしたクラスを
//  使えるようにする記述
require 'vendor/autoload.php';

//  Dotenv\Dotenv は Dotenv という名前空間
//  にある Dotenv というクラスという意味。
$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
try {
    $dotenv->load();
} catch (Dotenv\Exception\InvalidPathException $e) {
exit('.env ファイルが見つかりません')
} catch (Dotenv\Exception\InvalidFileException $e) {
    exit('.env ファイルの形式が正しくありません。');
}

echo $_ENV['DB_HOST']. "\n";
``` 

コマンドプロンプトで、`test-env.php` を実行します。

まず、どこのディレクトリにいるか確認
```cmd
cd
```
プロジェクトディレクトリにいない場合は移動
```cmd
cd C:\dev\project-name
```
`test-env.php` を実行
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

 C:\dev\ua-check\vendor のディレクトリ

2026/08/10  05:38               748 autoload.php
               1 個のファイル                 748 バイト
               0 個のディレクトリ  99,032,584,192 バイトの空き領域

C:\dev\ua-check>dir vendor\autoload.php
 ドライブ C のボリューム ラベルは Windows です
 ボリューム シリアル番号は B690-2094 です

 project-name のディレクトリ

2026/08/10  05:38               748 autoload.php
               1 個のファイル                 748 バイト
               0 個のディレクトリ  99,032,584,192 バイトの空き領域

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

### test-env.php と vendor の相対的な位置関係を確認
`test-env.php` で
```
require_once __DIR__ . '/vendor/autoload.php';
```
にように `autoload.php` を読み込んでいるので、

`require_once __DIR__ .` の後に実行ファイルがある場所から `vendor` ディレクトリへの相対パスを記入している必要があります。

### composer.json を確認
