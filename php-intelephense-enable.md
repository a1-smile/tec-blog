# PHP Intelephense settings.json での設定 | ショートカット競合を解決
# Intelephense に必要な設定を settings.json で行います。この方法も公式で書いてある方法の1つです。

# 入力補完のショートカット `Ctrl + Space` が Windows の入力切替 と競合している問題を解決

# Wordpress 開発環境でも使える設定



# 開発環境
OS:Windows11

PHP:8.3.1

テスト環境:MAMP
# 設定は以下のようにしました    

## ビルトインの Language Features for PHP fils は無効化せず、`setting.json` で機能調整

## 入力補完のショートカット `Ctrl + Space` が Windows の入力切替 と競合しているため、`Alt + /` に変更

## Wordpress の組み込み関数に対応させる

# 無料版ではできないこと
## `F2` でリネームシンボルはできない

## 関数が呼び出されている場所から、同じ関数が呼び出されている他の場所を調べることはできない

## require_once で読み込まれるファイル名をクリックして読み込み元に移動することはできない

## PHP DOC の生成はできない

## 関数定義の内容を折りたたむことはできない `Ctrl+ Shift+ [`(折りたたみ) | `Ctrl+ Shift+ ]`(展開)

# 無料版でできること
## `F12` 定義に移動、または候補を表示 `Alt + F12` で小さく表示
## `Shift + F12` 呼び出している場所、実装、参照先を表示
## `Alt + /` を入力補完に割り当て
## `Sift + Alt + F` インデント整形
## マウスオーバー 関数、変数の説明 必要な引数などを表示
## `Ctrl + T` 開いているフォルダ内のシンボルを検索して、移動（バックスラッシュを含む場合は不可）
## `Ctrl + Shift + O` 現在開いているファイル内のシンボルを一覧表示
## カーソルを置いたシンボルと同じシンボルをハイライトする。（現在のファイル内）
## カーソルを関数またはメソッドに合わせると引数などの情報が表示される
## HTML CSS JavaScript に対して基本的な補助機能がある
# 設定
設定は以下の 3 項目で行いました。
- ビルトイン拡張機能 `Language Features for PHP files` は有効のまま
- setting.json の設定で機能を調整
- コード補完機能のショートカットを変更
## ビルトイン拡張機能 `Language Features for PHP files` は有効のまま
公式の説明には、

Disable: Language Features for PHP files

Leave: PHP Language Basics

また、同様の機能を提供する拡張機能も OFF にする必要がある可能性があるかもしれません、と書いてあります。

しかし、一方で

** settings.json で一部の機能を無効にすることもできる **

とも書いてありますので、

`Language Features for PHP files`

は有効のままにして、`settings.json` で整合性を設定する、というのも正式な設定方法です。
Intelephense への理解が深まり、 settings.json の記入にも慣れるというメリットもあります。

## setting.json の設定で機能を調整
`setting.json` では以下の行4を追記します。

```json
// PHP の標準バリデーションを無効
"php.validate.enable": false,
```
```json
// フォーマットは Intelephense に指定
"editor.defaultFormatter": "bmewburn.vscode-intelephense-client",
```
```json
// 保存時に整形される
"editor.formatOnSave": true,
```
```json
// Language Features for PHP files の基本補完を無効化
"php.suggest.basic": false,
```

なお、`"php.validate.enable": false,` により
以下の 2 行は無視されます。
> 私の環境における例で、MAMP の PHP にパスを通してあります。
```json
// 以下の 2 行は  php.validate.enable: false の間は参照されない設定です。
"php.validate.executablePath": "C:\\MAMP\\bin\\php\\php8.3.1\\php.exe",
"php.validate.run": "onType",
```
しかし、不具合や設定ミスなどで、元に戻したい場合も想定して、この 2 行を、私は残しておきます。
##  入力補完のショートカット `Ctrl + Space` は Windows の 半角英数入力 と日本語入力 の切り替えと割り当てられているため、`Alt + /` に変更
入力補完は、`Ctrl + Space` のショートカットが Windows の日本語 IME の切り替えで使われているため `Alt + /` と設定しました。

> ⚠️ ショートカットキーを設定するときに
> `keyincomposition` と表示される場合は
> Windows の入力モードを「半角英数字」に切り替えます。

次の手順で設定しました。

1. `Ctrl+K` ➡️ `Ctrl+S` ショートカット一覧
2. 検索欄に `editor.action.triggerSuggest`
3. 鉛筆マーク クリック（ 項目にマウスをあてると鉛筆マークが表示されます ）
4. `Alt + /` を同時押し（ 同時に押さないと登録されません ）
5. `Enter`

# 無料版で使える機能

## `F12` 定義に移動、または候補を表示 `Alt + F12` で小さく表示
- 変数
- プロパティ
- 関数
- メソッド
- クラスのインスタンス
- インターフェイスの実装のインスタンス

にカーソルをあわせて `F12` をおすと、定義元へ移動。

複数候補がある場合は、ちいさな画面枠に候補を表示。

`Alt + F12` でちいさな画面枠で定義元を表示。

## `Shift + F12` 参照の検索
- 変数
- プロパティ
- 関数
- メソッド
- クラスのインスタンス
- インターフェイスの実装

にカーソルを合わせて `Shift + F12` をおすと、そのシンボルが使用されている場所を検索。

リファクタリングするときに、どこまで影響が及ぶかを調べられるので、便利です。

## `Ctrl + Shift + Space` を入力補完に割り当て
シンボル名を途中まで入力し、`Alt + /` をおすと、候補が表示されます。

関数名などをきちんと覚えていない場合に役立ちます。
NES で関数名を補完しようとすると、新しい関数名を作ってしまうことがあるので、役立ちます。

## `Sift + Alt + F` インデント整形
インデントは半角スペース 5 つ

定義の括弧は改行してからです。括弧の始まりと閉じの対応がわかりやすいです。

## マウスオーバー 関数、変数の説明 必要な引数などを表示
関数の機能や必要な引数

変数の型

などを説明。

# まとめ
- `F12` 定義に移動
- `Alt + F12` 定義を小さく表示
- `Shift + F12` 使われている場所を検索
- `Alt + /` を入力補完に変更
- `Shift + Alt + F` インデント整形
- マウスオーバー 説明

ショートカットの競合で戸惑いましたが、設定してしまえば便利に使えます。







    


