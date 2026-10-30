# PHP Intelephense 設定と使い方
# 開発環境
OS:Windows11

PHP:8.3.1

テスト環境:MAMP
# 設定
## ビルトインの Language Features for PHP fils は無効化せず、`setting.json` で機能調整

## 入力補完のショートカット `Ctrl + Space` が機能しなかったため、`Alt + /` に変更

# 無料版ではできないこと
## `F2` でリネームシンボルはできない

## PHP DOC の生成はできない

# 使い方
## `F12` 定義に移動、または候補を表示
## `Shift + F12` 呼び出している場所、実装を表示
## `Alt + /` を入力補完に割り当て
## `Sift + Alt + F` インデント整形
## マウスオーバー 関数の説明 必要な引数などを表示

# 設定
設定は以下の 3 項目で行いました。
- ビルトイン拡張機能 `Language Features for PHP files` は有効のまま
- setting.json の設定で機能を調整
- コード補完機能のショートカットを変更
## ビルトイン拡張機能 `Language Features for PHP files` は有効のまま
説明には、

Disable: Language Features for PHP files

Leave: PHP Language Basics

また、同様の機能を提供する拡張機能も OFF にしろと書いてあります。

しかし、

`Language Features for PHP files`

は有効のままにして、`settings.json` で整合性を設定します。

## setting.json の設定で機能を調整
`setting.json` では以下の 3 行を追記します。

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
以下の 2 行と同様な設定は無視されます。
```json
// 以下の 2 行は  php.validate.enable: false の間は参照されない設定です。
"php.validate.executablePath": "C:\\MAMP\\bin\\php\\php8.3.1\\php.exe",
"php.validate.run": "onType",
```
##  入力補完のショートカット `Ctrl + Space` が機能しなかったため、`Alt + /` に変更
私の環境では、入力補完のショートカットが機能しなかったため、

設定をしました。

また、私の知識不足でどうしても `Ctrl+Space` と設定できなかったため `Alt+/` と設定しました。

> ⚠️ ショートカットキーを設定するときに
> `keyincomposition` と表示される場合は
> Windows の入力モードを「半角英数字」に切り替えます。

次の手順で設定しました。

1. `Ctrl+K` ➡️ `Ctrl+S` ショートカット一覧
2. 検索欄に `editor.action.triggerSuggest`
3. 鉛筆マーク クリック（ 項目にマウスをあてると鉛筆マークが表示されます ）
4. `Alt+/` を同時押し（ 同時に押さないと登録されません ）
5. `Enter`

## `F12` 定義に移動、または候補を表示
- 関数
- メソッド
- クラスのインスタンス
- インターフェイスの実装

にカーソルをあわせて `F12` をおすと、定義元へ移動。

複数候補がある場合は、ちいさなウィンドウに候補を表示





    


