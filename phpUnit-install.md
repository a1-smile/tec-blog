# PHPUnit のインストール時につまづいた点について共有します。

## 開発環境
OS: Windows 11 Home
ローカル環境: MAMP (PHP 8.3.1)
Composer: 2.10.2

## エラーの内容
発生したエラーの概要は、以下の通りです。
- コマンドプロンプトでは、「^」は特殊文字です。
- 一方で、Composer では、「^12」は「12系で互換性のある最新バージョンをインストールする」という意味で使用されます。
- コマンドプロンプトで
`composer require --dev phpunit/phpunit:^12`
のようにバージョン指定をすると、
「^」が特殊文字として認識され、
`composer require --dev phpunit/phpunit:12` と、
12 系ではなく 12.0.0 を指定していると認識されます。
- PHPunit 12.0.0 にはセキュリティー勧告がされていて、Composer は
それを理由にインストールを拒否

## 解決法
コマンドプロンプトで「^」を特殊文字として扱わないようにすればよい。
方法は二つあります。
1. 「^」を含む引数の文字列を「\"」で囲む。
```
composer require --dev "phpunit/phpunit:^12"
```
コマンドプロンプトでは、コマンドの引数を「\"」で囲むと、
特殊文字を無効化し、
文字列をそのまま引数として渡すことができます。

>`composer require --dev phpunit/phpunit:^12`
この場合の引数とはなにか？
と疑問に思うかもしれません。
- composer: 実行するコマンド名
- require: Composer のサブコマンド
- --dev: 開発用依存関係として追加するためのオプション
- phpunit/phpunit:^12: Composer に渡す引数。追加したいパッケージ名とバージョン条件です
2. 「^」を「^^」に置き換える。
```
composer require --dev phpunit/phpunit:^^12
```
コマンドプロンプトでは、「^」は、特殊文字をエスケープする文字として使用されます。
ややっこしいですが、
「^」をエスケープするために「^」を使用して「^^」とすることで、
コマンドプロンプトで正しく解釈されます。
    

## 状況の説明
私が使用している MAMP の PHP は 8.3.1 です。
この PHP で使用できる PHPUnit のバージョンを調べたところ
 `PHPUnit 12 系`
を使用できることがわかりました。
そこで、PHPUnitのバージョンを指定してインストールを試みました。
```
composer require --dev phpunit/phpunit:^12
```
すると、以下のようなエラーが発生しました。
```
The "12" constraint for "phpunit/phpunit" appears too strict and will likely not match what you want.
```
12 系ではなく 12.0.0 を指定していると認識されました。
```
```
See https://getcomposer.org/constraints.
```
このリンク先を参照しろ、ということなので、
みてみますと
`Note: If you are using PowerShell on Windows, you have to escape carets when using them as argument on the CLI for example when using the composer require command. You have to use four subsequent caret operators, e.g. ^^^^1.2.3, to ensure the caret operator gets passed to Composer correctly.
`
PowerShell では、コマンドラインで「^」を引数として使用する場合は、エスケープする必要があるようです。
コマンドプロンプトでも、おなじようなことが起きるのではないかと考えました。
そこで、コマンドプロンプトで「^」はどのような意味を持つのか調べてみました。
コマンドプロンプトでは、「^」は特殊文字で、次の文字をエスケープするために使用されるということがわかりました。

もう一方で、
- Root composer.json requires phpunit/phpunit 12 (exact version match: 12, 12.0, 12.0.0 or 12.0.0.0), found phpunit/phpunit[12.0.0] but these were not loaded, because they are affected by security advisories
```
12.0.0 にはセキュリティー勧告がされていて、Composer はそれを理由にインストールを拒否するようです。

そこで、コマンドプロンプトで「^」を特殊文字として扱わないようにすればよいのではないかと考えました。
引数を「\"」で囲むことで、特殊文字を無効化し、
文字列をそのまま引数として渡すことができます。
```
composer require --dev "phpunit/phpunit:^12"
```
を実行すると、

`C:\dev\ua-check>vendor\bin\phpunit --version PHPUnit 12.5.33 by Sebastian Bergmann and contributors.`
