# vlucas/phpdotenv インストール

## 開発環境
- OS: Windows 11
- ローカル環境: MAMP
- PHP バージョン: 8.3.1
- Composer バージョン: 2.10.2
## 背景となる知識
PHP では、

環境ごとに異なる設定情報を管理する必要があります。

（データベース接続情報や API キーなど）

これらの情報をコードに直接記述すると、
セキュリティリスクが高まるだけでなく、
環境ごとに設定を変更するのが手間に。

そこで、`.env` ファイルを使用して環境変数を管理する方法が推奨されます。

`vlucas/phpdotenv` は、この `.env` ファイルを読み込み、
PHP の環境変数として利用できるようにするライブラリです。

## 準備 .env のファイルの設定
.env ファイルの設定方法と使用法については、

以下の記事を参考にしてください。

[.env のファイルの設定と使用方法](<https://qiita.com/a1-smile/items/eca7ce8aa9cccfb90ed9>)

 ⚠️**注意**
 .env は git 管理しないように気を付けます。
```text
project-name/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
```

プロジェクト ディレクトリのルートに `.gitignore` ファイルを作成。

.gitignore に
```gitignore
.env
/vendor/   
```
と記述します。

( vendor ディレクトリは、通常は git 管理しません。
composer.lock をコミットしておけば各環境で composer install により同じ依存関係を復元できるためです。composer.json と composer.lock は Git 管理します。)

`.env` ファイルには機密情報が含まれるため、

この作業の前に .env ファイルを作ってコミットをしないように注意します。

git の追跡対象になってしまいます。

すでに、
`git status` で .env が表示されている場合は、

```cmd
dir /a .env
```
として、
```cmd
ドライブ C のボリューム ラベルは Windows です
 ボリューム シリアル番号は B690-2094 です

 C:\dev\ua-check のディレクトリ

2026/09/04  04:03                66 .env
               1 個のファイル                  66 バイト
               0 個のディレクトリ  103,143,612,416 バイトの空き領域
```

のように、`.env` が表示されることを確認して、
```cmd
git ls-files --error-unmatch .env
```
を実行、
```cmd
error: pathspec '.env' did not match any file(s) known to git
Did you forget to 'git add'?
```
とエラーが表示される場合は未追跡です。

`.gitignore` に `.env` を追加すれば git 管理されません。

一方で、エラー表示されない場合は、git の追跡対象になっているので、

まず、`.gitignore` に `.env` を追加して、
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

また、
 サンプルとして、
 `.env.example` ファイルも作成します

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

`.env.example` ファイルの内容は以下のようにします。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=
```

## 設定手順の概要
### PHP のバージョン確認
`vlucas/phpdotenv` をインストールするには PHP のバージョンを確認が必要。

プロジェクトディレクトリで動くPHP のバージョンが

`vlucas/phpdotenv` が必要としている PHP の範囲にあるかを確認。

以下のリンクから確認できます。
[The PHP Package Repository](https://packagist.org/packages/vlucas/phpdotenv?)

この記事を書いた時点では
```
Requires

php: ^7.2.5 || ^8.0
ext-pcre: *
graham-campbell/result-type: ^1.2
phpoption/phpoption: ^1.10
symfony/polyfill-ctype: ^1.26
symfony/polyfill-mbstring: ^1.26
symfony/polyfill-php80: ^1.26
```
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
cd <project-nameへのパス>
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
DB_PASSWORD=<パスワードを記入してください>
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

//  必須の設定値を確認する場合は、以下のように記述します。
try {
$dotenv->required([
    'DB_HOST',
    'DB_NAME',
    'DB_USER',
    'DB_PASSWORD',
    ])->notEmpty();
} catch (Dotenv\Exception\ValidationException $e) {
    error_log($e->getMessage());
    exit('必須の環境変数が設定されていません: ');
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
cd <project-nameへのパス>
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

OS／Web サーバー側ですでに同名の環境変数があるとそちらを優先する設定になっているので、注意が必要です。

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
cd <project-name のパス>
```
とプロジェクト ディレクトリに移動して、
```
dir vendor\autoload.php
```
を実行して、
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

```
などと表示されれば、`vlucas/phpdotenv` は正しくインストールされています。
（ 実行結果は環境によって異なります。）
### composer.json を確認
`composer.json` に
```json
"require": {
        "vlucas/phpdotenv": "^x.x"
    }
```
のように記述されているかを確認します。
x.x はプレースホルダーで、
たとえば、私の環境ですと、
```json
 "require": {
        "vlucas/phpdotenv": "^5.7"
    }
```
となっています。
## test-env.php と vendor の相対的な位置関係を確認
test-env.php での記述で
```php
require_once __DIR__ . '/vendor/autoload.php';
```
のように `autoload.php` を読み込んでいるので、

`require_once __DIR__ .` の後に

コードが書いてあるファイルのディレクトリを基準にして、

 `vendor/autoload.php` 
 
 へのパスを記入している必要があります。

（上の記述は、`vendor`ディレクトリが同じ階層にある場合の書き方です。）

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

上の書き方ですと、PHP が `Dotenv\Dotenv\Dotenv` を探してしまい、
```
C:\dev\ua-check>php test-env.php PHP Fatal error: Uncaught Error: Class "Dotenv\Dotenv\Dotenv" not found in C:\dev\ua-check\test-env.php:6 Stack trace: #0 {main} thrown in C:\dev\ua-check\test-env.php on line 6
```
のように、

`Class "Dotenv\Dotenv\Dotenv" not found`

という意味のエラーが発生します。

### "Dotenv\Dotenv" not found というエラーの場合 1
"Dotenv\Dotenv" not found というエラーメッセージが表示された場合は、

vlucas/phpdotenv が未インストール、

vendor が存在しない

という可能性があります。

```
project-name\
│
├─ .env
├─ composer.json
├─ composer.lock
├─ test-env.php
```

を確認して、

`composer.lock` 内を検索して、
```
 "name": "vlucas/phpdotenv",
```
という記述があるばあいは、

プロジェクトフォルダに移動して、コマンドプロンプトで、
```cmd
composer install
```
とすれば、`vlucas/phpdotenv` をインストールできます。

誤って `vendor` を削除してしまった場合も再インストールされます。

### "Dotenv\Dotenv" not found というエラーの場合 2

「test-env.php と vendor の相対的な位置関係を確認」

という項目ですでに説明しましたが、

test-env.php での記述で
```php
require_once __DIR__ . '/vendor/autoload.php';
```
の部分で読み込むパスが違う

という場合があります。

### "Dotenv\Dotenv" not found というエラーの場合 3

`require_once` のパスが正しいかを確認

`composer install` を実行してさらにエラーになるばあいは、

Composerのオートローダーを再生成してみます。
コマンドプロンプトで、
```cmd
composer dump-autoload
```
を実行します。

### .env ファイルが正しく設定されているかを確認
以下のようなエラーメッセージが表示される場合は、

`.env` を読みこめていません。
```
C:\dev\ua-check>php test-env.php PHP Fatal error: Uncaught Dotenv\Exception\InvalidPathException: Unable to read any of the environment file(s) at [project-name\.env]. in project-name\vendor\vlucas\phpdotenv\src\Store\FileStore.php:68 Stack trace: #0 project-name\vendor\vlucas\phpdotenv\src\Dotenv.php(222): Dotenv\Store\FileStore->read() #1 project-name\test-env.php(7): Dotenv\Dotenv->load() #2 {main} thrown in project-name\vendor\vlucas\phpdotenv\src\Store\FileStore.php on line 68 project-name>php test-env.php
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

また、VsCode でテキストファイルとして認識させようとして、 `.txt` と拡張子をつけてしまうというミスもありえます。

確認するには、コマンドプロンプトで、プロジェクトディレクトリ直下で
```cmd
dir /a
```
を実行します。
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
cd <project-nameへのパス>
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

ファイル構成や、

実際に環境変数を読み込めるかを確認します。








