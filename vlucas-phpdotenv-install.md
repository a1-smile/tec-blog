# vlucas/phpdotenv インストール [ `.env` を読み込むライブラリ ]
## 結論
`vlucas/phpdotenv` をインストールする手順は
✅ `vlucas/phpdotenv` が必要とする PHP バージョンを確認
コマンドプロンプトで、
```cmd
composer show --all vlucas/phpdotenv
```
を実行

requires の欄を確認
✅ プロジェクトのディレクトリへ移動
```cmd
cd <project-nameへのパス>
```
✅  PHPのバージョンを確認
```cmd
where php
```
```cmd
php -v
```
`vlucas/phpdotenv` が要求するバージョンであることを確認

>Windows PC で PHP のパスが設定されていれば、どのディレクトリにいても、同じ PHP が実行されます。

✅  Composerが正常に動くことを確認
```cmd
composer --version
```
⚠️ **composer.json がないときは以下のコマンドはエラーになります。**
Composer が使用する PHP を確認
```cmd
composer check-platform-reqs
```
✅  インストールコマンドを実行
```cmd
composer require vlucas/phpdotenv
```
✅  インストールと動作の確認

ファイル構成や、

実際に環境変数を読み込めるかを確認します。
## 開発環境
- OS: Windows 11
- ローカル環境: MAMP
- PHP バージョン: 8.3.1
- Composer バージョン: 2.10.2
## 背景となる知識
PHP では、

環境ごとに異なる設定情報（データベース接続情報など）
を管理する必要があります。

これらの情報をコードに直接記述すると、
- セキュリティリスクが高まる
- 環境ごとに設定を変更するのが手間

そこで、`.env` ファイルを使用して環境変数を管理する方法が推奨されます。

`vlucas/phpdotenv` は、この `.env` ファイルを読み込み、
PHP の環境変数として利用できるようにするライブラリです。

## 準備 : `.env` ファイルを設定する
`.env` ファイルの設定方法と使用法については、

以下の記事を参考にしてください。

[`.env` のファイルの設定と使用方法](<https://qiita.com/a1-smile/items/eca7ce8aa9cccfb90ed9>)

重要な部分を以下で説明していきます。

 ⚠️**注意**
 `.env` は git 管理しないように気を付けます。
```text
project-name/
  ├─ .gitignore
  ├─ .env
  ├─ .env.example
```

プロジェクト ディレクトリのルートに `.gitignore` ファイルを作成。

.gitignore に以下の記述をします。
```gitignore
.env
/vendor/   
```
> vendor ディレクトリは、通常は git 管理しません。
> composer.lock をコミットしておけば各環境で `composer install` により同じ依存関係を、通常は復元できるためです。
> 一方で `composer.json` と `composer.lock` は Git 管理し、コミット対象にします。

> なお、`/vendor/` はプロジェクトルートの `vendor` のみを対象にします。

`.env` ファイルには機密情報が含まれるため、

安全策として

`.env` を作成する前に .gitignore を設定することを推奨します。

誤って `.env` を ステージング や コミット してしまうミスを防ぐためです。

`.env` が Git の追跡対象ではなく、ignore されている状態になっていることを確かめるには、

次の 3 項目を確認します。
以下が満たされていれば、通常の操作では `.env` はコミットされません。
1. `.env` が正しい場所に、正しい名前で設定されている
2. `.env` が `.gitignore` に登録されている
3. `.env` が git 追跡対象ではない

補助として、
 `git status --short` を実行して `.env` が表示されないかを確認すると良いです。現在の変更や、未追跡ファイルが表示されます。

> `.gitignore` に登録されていても、過去に git の追跡対象になっていると、追跡対象から外されません。`gitignore` は新しいファイルやフォルダを追跡対象にするかを判断しているだけだからです。

✅ `.env` が正しい場所に、正しい名前で設定されているかを確認します。

プロジェクトルートにおいて、コマンドプロンプトで以下を実行します。

```cmd
dir /a .env
```
> dir はファイルとフォルダを一覧表示します。
> /a は隠しファイルとシステムファイルも表示するオプションです。

実行結果に `.env` が表示されれば、`.env` が正しく作成されています。

表示されなければ、以下のことがないか確認します。
- `.env` を設定する場所を間違えている
- `.env` に.txt などの拡張子が付いている

✅ `.env` が `.gitignore` に登録されているかを確認します。

```cmd
git check-ignore -v --no-index .env
```
> -v は verbose（詳細表示） の指定です。

> `--no-index` は、Git の追跡対象かどうかを考慮せず、指定したパスが ignore ルールに一致するかを確認するオプションです。

> また、git check-ignore の出力は「そのファイルが実際に無視されている」というより、--no-index 使用時には .gitignore のパターンに一致することを確認しています。

実行結果が
```cmd
.gitignore:x:.env       .env
```
と表示されれば、`.gitignore` の x 行目にある `.env` というパターンに、指定した `.env` が一致しています。
しかし、過去に追跡対象となっているファイルは、継続的に追跡対象のままです。

> x はプレースホルダーで、行番号が表示されます。


✅ `.env` が git 追跡対象ではないことを確認します。
コマンドプロンプトでプロジェクトルートにおいて、以下にコマンドを実行します。
```cmd
git ls-files --error-unmatch -- .env
```
> `git ls-files` は git 管理しているファイルを一覧するコマンド
> `--error-unmatch` は指定したファイルが見つからなかったらエラーにする
> つまり `.env` が git 管理されていなかったらエラー

> `.env` の前の `--` は、オプション指定の終わりを示す区切りです。

を実行、
コマンドがエラー終了し、`.env` が出力されなければ、`.env` は追跡対象ではありません。

`.env` が出力されて正常終了した場合は、追跡対象です。

✅ 補助として`git status --short` を実行して `env` が表示されないことを確認すると良いです。
```cmd
git status --short
``` 
ここで `.env` が表示されている場合は、以下の2つの可能性があります。

- .gitignore に登録されていない
- .gitignore に登録されているが、すでに追跡対象になっているので .gitignore が効かない

表示例は、3つあります。
?? `.env` と表示された場合、`.env` は未追跡かつ無視されていません。

M(半角スペース) `.env` と表示された場合は `git add` されています。
```cmd
M  .env
```

(半角スペース)M `.env` と表示された場合は `git add` されていません。
```cmd
 M .env
 ```


.env が Git の追跡対象になっている場合は

まず、`.gitignore` に `.env` を追加して、

`.gitignore` をステージング
```cmd
git add .gitignore
```

`.env` を追跡対象から外す
```cmd
git rm --cached -- .env
```
そして、コミット
```cmd
git commit -m "Remove .env from tracking"
```
として、git の追跡対象から外します。
> `git rm --cached -- .env` は、ローカルの `.env` ファイルは残したまま、Git の追跡対象から外すだけです。
>**⚠️重要**
>.gitignore は
>「まだ追跡されていない `.env`」だけを無視します。
>
>すでにコミット済みの機密情報は、
>
>git rm --cached .env で追跡解除しても
>
>Git の過去履歴には残ります。

> すでに push 済みの場合、履歴からファイルを削除しても、取得済みのコピーやキャッシュから秘密情報を完全に回収できるとは限りません。
>
> まず DB パスワード、API キー、トークンなどを無効化または変更（ローテーション）してください。
> その後、必要に応じて Git の履歴から `.env` を削除します。

>**⚠️注意**
>`.env` を作ったつもりが、`.env.txt` として保存されていることもあります。
>`.env.txt `として保存された場合は、`.env` として認識されず、
>git にも普通のファイルだと認識されて、コミットされてしまう可能性があります。

>同様に .gitignore にも .txt などの拡張子を付けないようにきをつけます。

> Windows では、エクスプローラーの「表示」から「ファイル名拡張子」を有効にすると、`.env.txt` になっていないか確認しやすくなります。



 `.env` ファイルの内容は、
例えば PDO を使用したデータベース接続情報なら、以下のように記述します。
```env
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_user_name
DB_PASSWORD=<パスワードを記入>
```
また、
 サンプルとして、
 `.env.example` ファイルも作成します
 
`.env.example` ファイルには必要な環境変数の名前と記入例を記述し、

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
### `vlucas/phpdotenv` が必要とする PHP のバージョンを確認
コマンドプロンプトで、
```
composer show --all vlucas/phpdotenv
```
を実行し、requires の欄を確認。

または、
以下のリンクから確認できます。
[The PHP Package Repository](https://packagist.org/packages/vlucas/phpdotenv?)

### 実行環境で動く PHP のバージョンを確認
`vlucas/phpdotenv` をインストールするには PHP のバージョン確認が必要。

実行環境で以下を実行。
```cmd
where php
```
```cmd
php -v
```
確認した PHP のバージョンが

`vlucas/phpdotenv` が必要としている PHP の範囲にあるかを確認。

>Windows PC で、環境変数として PHP にパスを通した PHP のバージョンが使われます。

### Composer を確認する
プロジェクトディレクトリで Composer が動くかを確認。

Composer が使用する PHP を確認。

### インストールコマンド実行
Composer で vlucas/phpdotenv をインストールするコマンドを実行。

### 動作確認
vlucas/phpdotenv で、環境変数を読み込めるかを確認。

概要は以上です、具体的なインストールを説明していきます。

## 具体的なインストール手順

### `vlucas/phpdotenv` が必要とする PHP バージョンを確認
>必要とする PHP バージョンは、コマンドプロンプトで、
```cmd
composer show --all vlucas/phpdotenv
```
>を実行して、`requires` の欄を確認します。
>例えば、この記事の執筆時では以下です。
```cmd
requires
php ^7.2.5 || ^8.0
ext-pcre *
graham-campbell/result-type ^1.2
phpoption/phpoption ^1.10
symfony/polyfill-ctype ^1.26
symfony/polyfill-mbstring ^1.26
symfony/polyfill-php80 ^1.26
```
>つまりこの記事を書いた時点では、

`PHP:^7.2.5 || ^8.0`

>が必要です。

ただし、composer show --all vlucas/phpdotenv はパッケージの候補情報を表示するものであり、プロジェクトの依存関係や PHP バージョンを考慮して、最終的にどのバージョンが選ばれるかを完全に示すものではありません。

### vlucas/phpdotenv を使用するディレクトリまで移動
コマンドプロンプトを使用して、
`cd` コマンドでプロジェクトのルートディレクトリに移動します。
```cmd
cd <project-nameへのパス>
```

### PHP のバージョン確認
`php -v` コマンドで PHP のバージョンを確認します。
```cmd
where php
```
でどの php が使われるかを確認して
```cmd
php -v
```
>ここで、`vlucas/phpdotenv` が要求する PHP バージョンが表示されることを確認します。

記事を執筆時点での
`php -v`
の実行結果例を示します。
```
PHP 8.3.1 (cli) (built: Jan 16 2024 11:57:11) (ZTS Visual C++ 2019 x64)
Copyright (c) The PHP Group
Zend Engine v4.3.1, Copyright (c) Zend Technologies
```
`PHP 8.3.1` なので、
`PHP:^7.2.5 || ^8.0`

という条件をみたしています。
### Composer が動くことを確認

`composer --version` コマンドで Composer のバージョンを確認します。
```cmd
composer --version
```
次のように Composer のバージョンが表示されれば問題ありません。
```cmd
Composer version 2.10.2 2026-07-01 11:24:45
PHP version 8.3.1 (C:\MAMP\bin\php\php8.3.1\php.exe)
Run the "diagnose" command to get more detailed diagnostics output.
```

念のため次のコマンドでも、

Composer が使用する PHP を確認しておきます。


⚠️ **composer.json がないときは以下のコマンドはエラーになります。**

```cmd
composer check-platform-reqs
```
### vlucas/phpdotenv のインストール
Composer を使用してインストールします。

```cmd
composer require vlucas/phpdotenv
```
を実行します。

## インストールと動作の確認

プロジェクトのルートディレクトリに `.env` ファイルを設定。

環境変数を定義します。

例えば、MAMP のローカル環境で、 PDO 接続情報ですと以下のようになります。

```env
DB_HOST=localhost
DB_NAME=<データベース名>
DB_USER=root
DB_PASSWORD=<パスワード>
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
use Dotenv\Dotenv;
use Dotenv\Exception\InvalidFileException;
use Dotenv\Exception\InvalidPathException;
use Dotenv\Exception\ValidationException;

$dotenv = Dotenv::createImmutable(__DIR__);
try {
    $dotenv->load();
} catch (InvalidPathException $e) {
exit('.env ファイルが見つかりません');
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

⚠️**注意** 

`createImmutable` はすでに設定してある環境変数を変更しないことに注意してください。


OS／Web サーバー側などで、すでに同名の環境変数が設定してある場合は既存値を上書きしません。

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
versions : * v5.x.x
[* v5.x.x はプレイスホルダーで私の環境では v5.7.0 と表示されます。]
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
⚠️**注意**
説明した記述がないときには、`composer.json` を手動編集せずに、

コマンドプロンプトでプロジェクトディレクトリ直下に移動して以下のコマンドを実行します。
```cmd
composer require vlucas/phpdotenv
```
このコマンドにより依存関係が追加されます。
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
```php
use Dotenv\Dotenv;

$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
```
と書くのは、間違いです。
```php
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

または、

vendor が存在しない

という可能性があります。

これらの場合は、

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

`require_once` のパスが正しいかを確認して、さらに

`composer install` を実行してもなおエラーになる場合は、

「vendor を直接操作した場合や、autoload 定義を変更した場合に試す補助的な手段」として、

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

無い場合は、`test-env.php` と同じ階層に `.env` を作成する。

`.env` に拡張子 `.txt` などがついている場合はとりのぞく。

意図せずに、`.txt` という拡張子が付いてしまうことがあります。

例えば、

Windowsではメモ帳などで、

`.env`

と保存したつもりでも、

`.env.txt`

になっていることがあります。

また、VsCode でプレーンテキストとして認識させようと、 `.txt` と拡張子をつけてしまうというミスもありえます。

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

✅ `vlucas/phpdotenv` が必要とする PHP バージョンを確認
>要求する PHP バージョンは、コマンドプロンプトで、
```cmd
composer show --all vlucas/phpdotenv
```
>を実行して、`requires` の欄を確認します。

✅  プロジェクトのディレクトリへ移動
```cmd
cd <project-nameへのパス>
```
✅  PHPのバージョンを確認
```cmd
php -v
```
✅  Composerが正常に動くことを確認
```cmd
composer --version
```
✅  インストールコマンドを実行
```cmd
composer require vlucas/phpdotenv
```
✅  インストールと動作の確認

ファイル構成や、

実際に環境変数を読み込めるかを確認します。

## 補足
### vlucas/phpdotenv を使うと、必ず getenv() で読めるわけではありません

vlucas/phpdotenv が環境変数を読み込むライブラリとして説明しました。

しかし、createImmutable() の標準的な使い方では、
主に次の配列に値が入ります。
```php
$_ENV['DB_HOST'];
$_SERVER['DB_HOST'];
```
一方、次のコードで取得できるとは限りません。
```php
getenv('DB_HOST');
```
getenv() でも取得したい場合は、putenv() を使用する設定を明示的に選ぶ必要があります。


### .env の場所はcreateImmutable() に渡したパスで決まります

`vlucas/phpdotenv` で

.env は、`project\.env` に置き、

createImmutable() の記述
```php
$dotenv = Dotenv::createImmutable();
```
は、project\src\test-env.php にあるなら、

createImmutable() の引数はどのように書いたら良いか疑問に思うかもしれません、答えは
```php
$dotenv = Dotenv::createImmutable(dirname(__DIR__));
$dotenv->load();
```
です。

なぜ `dirname(__DIR__)` なのでしょうか？

ディレクトリ構成を次のように考えます。
```
project/
├── .env
├── vendor/
└── src/
    └── test-env.php
```
順番に説明します。

`__DIR__` は、現在の PHP ファイルが置かれているディレクトリのパスを表します。

今回は project\src です。

`dirname(__DIR__)` は、その1つ上のディレクトリを取得します。

ここでは project です。

Dotenv::createImmutable() の第1引数には、`.env` が置かれているディレクトリを指定します。

したがって、`dirname(__DIR__)` を指定すれば、`project\.env` を読み込めます。

ひとつ上の階層に上がるには、`dirname(__DIR__)` を使用します。

### composer.lock に記述があれば、必ずインストールできるとは限りません

composer.lock の説明 では、composer.lock に vlucas/phpdotenv があれば composer install でインストールできると説明しました、しかし、

通常は正しいですが、次の条件も必要です。

対応する composer.json がある

現在の PHP バージョンが要件を満たす

必要な PHP 拡張が有効

Composer がパッケージを取得できる

composer.lock が壊れていない

プラットフォーム要件を満たしている

特に PHP のバージョンや拡張機能が不足している場合、composer.lock に記述があってもインストールは失敗します。

### vendor\autoload.php が存在するだけでは、phpdotenv のインストール確認にはなりません

autoload.php の確認 で、次を確認しています。
```cmd
dir vendor\autoload.php
```
しかし、
これは Composer のオートローダーが存在することの確認にしかなりません。

phpdotenv のインストール確認には、次のコマンドを実行します。
```cmd
composer show vlucas/phpdotenv
```