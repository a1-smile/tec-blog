# PHPUnit のインストール時につまづいた点について共有します。

## 開発環境
OS:         Windows 11 Home
ローカル環境: MAMP (PHP 8.3.1)
Composer:   2.10.2

## エラーの内容
発生したエラーの概要は、以下の通りです。
コマンドプロンプトで
```
composer require --dev phpunit/phpunit:^12
```
を実行。
Composer はインストールを拒否。

## 結論

コマンドプロンプトでは `^` が特殊文字として処理されます。
Composer に `^` を含む引数を渡すには、次のいずれかを使います。
引数を二重引用符で囲む。
```cmd
composer require --dev "phpunit/phpunit:^12"
```
または、
`^` を `^^` に置き換えます。
```
composer require --dev phpunit/phpunit:^^12
```

## 予備知識
■ コマンドプロンプトでは、`^` は特殊文字で、次の文字をエスケープするために使用されます。
■ コマンドプロンプトでは、コマンドの引数全体を「\"」で囲むと、
特殊文字を無効化し、
文字列をそのまま引数として渡すことができます。

>`composer require --dev phpunit/phpunit:^12`
この場合の引数とはなにか？
と疑問に思うかもしれません。
- composer: 実行するコマンド名
- require: Composer のサブコマンド
- --dev: 開発用依存関係として追加するためのオプション
- phpunit/phpunit:^12: Composer に渡す引数。追加したいパッケージ名とバージョン条件です

■ Composer では、`^` は「バージョンの範囲を指定してインストールする」という意味で使用されます。



## 状況の説明
私が使用している MAMP の PHP は 8.3.1 です。
この PHP で使用できる PHPUnit のバージョンを調べたところ
 `PHPUnit 12 系`
を使用できることがわかりました。
そこで、PHPUnitのバージョンを指定してインストールを試みました。

私の場合は、開発フォルダー(ua-check)を MAMP の外において管理したいので、

```

C:\dev\ua-check          ← 実体（ここで開発・Git管理）
        ↑
        │ シンボリックリンク
        │
C:\MAMP\htdocs\ua-check  ← MAMP/Apacheから見える場所

```
という構成にしています。
そこで、コマンドプロンプトを開き
```
cd /d C:\dev\ua-check
```
と開発フォルダーに移動してから、以下のコマンドを実行しました。
```
composer require --dev phpunit/phpunit:^12
```
すると、以下のようなエラーが発生。
```
The "12" constraint for "phpunit/phpunit" appears too strict and will likely not match what you want.
```
「phpunit/phpunit に対する "12" というバージョン制約は厳しすぎます。おそらく意図どおりの指定ではありません。」

```
See https://getcomposer.org/constraints.
```
このリンク先を参照しろ、ということなので、
みてみますと
`Note: If you are using PowerShell on Windows, you have to escape carets when using them as argument on the CLI for example when using the composer require command. You have to use four subsequent caret operators, e.g. ^^^^1.2.3, to ensure the caret operator gets passed to Composer correctly.
`
>⚠️こちらは PowerShell での注意点です。
PowerShell では、コマンドラインで「^」を引数として使用する場合は、エスケープする必要があるようです。

>⚠️私が実行に使用しているのは、コマンドプロンプトです。

コマンドプロンプトでも、おなじようなことが起きるのではないかと考えました。
そこで、コマンドプロンプトで「^」はどのような意味を持つのか調べてみました。
コマンドプロンプトでは、「^」は特殊文字で、次の文字をエスケープするために使用されるということがわかりました。

Composer には以下コマンドが渡されたとかんがえられます。
```
composer require --dev phpunit/phpunit:12
```
この場合、
Composer は 12 系の最新バージョンを指定指定しているのではありません。
12.0.0 相当の完全一致制約として扱われます。そのため、12.5.33 のような後続バージョンは選択されません。
12.0.0 をインストールしようとします。

もう一方で、
- Root composer.json requires phpunit/phpunit 12 (exact version match: 12, 12.0, 12.0.0 or 12.0.0.0), found phpunit/phpunit[12.0.0] but these were not loaded, because they are affected by security advisories
```
12.0.0 にはセキュリティー勧告がされていて、
この環境の Composer 設定では、セキュリティ勧告の対象であるため PHPUnit 12.0.0 は依存関係の解決対象から除外されました。
以下、エラーメッセージ全文です。
```
C:\dev\ua-check>composer require --dev phpunit/phpunit:^12
The "12" constraint for "phpunit/phpunit" appears too strict and will likely not match what you want. See https://getcomposer.org/constraints
./composer.json has been created
Running composer update phpunit/phpunit
Loading composer repositories with package information
Updating dependencies
Your requirements could not be resolved to an installable set of packages.
Problem 1
- Root composer.json requires phpunit/phpunit 12 (exact version match: 12, 12.0, 12.0.0 or 12.0.0.0), found phpunit/phpunit[12.0.0] but these were not loaded, because they are affected by security advisories ("PKSA-z3gr-8qht-p93v"). Go to https://packagist.org/security-advisories/ to find advisory details. To ignore the advisories, add their IDs to the "policy.advisories.ignore-id" config or add the package to "policy.advisories.ignore". To turn the feature off entirely, you can set "policy.advisories.block" to false.
Installation failed, deleting ./composer.json.
```
そこで、コマンドプロンプトで「^」を特殊文字として扱わないようにすればよいのではないかと考えました。
引数を「\"」で囲むことで、特殊文字を無効化し、
文字列をそのまま引数として渡すことができます。
```
composer require --dev "phpunit/phpunit:^12"
```
を実行すると、

`C:\dev\ua-check>vendor\bin\phpunit --version PHPUnit 12.5.33 by Sebastian Bergmann and contributors.`
PHPUnit 12.5.33 がインストールされました。

開発用のフォルダーには、
概ね以下のようなセグメントが作成されました。
```
C:\dev\ua-check
    ├─vendor
    │  └─bin
    │     └─ phpunit
    ├─composer.json
    └─composer.lock
```

## 簡単なテストコードを試してみる
開発フォルダー直下に
`tests` フォルダーを作成し、
その中に `ExampleTest.php` というファイルを作成します。
以下のコードを記述します。

```php
<?php
use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function testExample()
    {
        $this->assertSame(2, 1 + 1);
    }
}
```
コマンドプロンプトで、開発フォルダーに移動してから、以下のコマンドを実行します。
```
vendor\bin\phpunit tests
```
```
OK (1 test, 1 assertion)
```
という結果が返ってきました。

phpUnit のインストールと、簡単なテストコードの実行ができました。

## まとめ
- コマンドプロンプトでは、「^」は特殊文字です。
- 一方で、Composer では、「^」は「バージョンの範囲を指定してインストールする」という意味で使用されます。
- コマンドプロンプトにおいて Composer で「^」を使用する場合は、引数を「\"」で囲むか、「^」を「^^」に置き換える必要があります。
