---
title: "ソースコードを綺麗にするためにまず心がけたい３点"
date: 2019/06/10 09:20:47
postid: ""
tag:
  - コーディング規約
  - コードレビュー
  - 可読性
category:
  - Programming
author: 王紹宇
lede: "若手中心の開発チームで1~2年ほど過ごしました。その時の経験を通して、こうすればもっとソースコードが綺麗になると感じたことを3点まとめます。"
---

こんにちは、テクノロジーイノベーショングループ所属、社会人歴4年目エンジニアの王紹宇です。大学ではC/C++使いでしたが、最近はPythonとJavaScript に注力しています。

現在（2019年6月）、とある業務システムの開発チームに所属し、ある実験的な機能を素早くリリースしてはユーザからのフィードバックを得て改善、それをまた次回以降のリリースに入れる、といった業務システムでは珍しくアジャイル的なスタイルでここ1~2年ほど過ごしています。採用技術は主にReact/Next.js/Go/GCPです。

チームメンバーはフューチャーに新卒入社してから1~5年目という、若手中心でガンバっています。

この若いチームでの開発経験を通して、こうすればもっとソースコードが綺麗になると感じたことを3点まとめます。


# ソースコードを綺麗にする３つのポイント

📖 綺麗なエッセイを読んだら目に優しく、心も癒やされますよね。
💻 ソースコードもエッセイのように美しくすると非常に価値が上がると思います。

本エントリーでは、将来的にどこかに展開しやすい**可読性が高い**コードを綺麗であり高価値であるとします。

可読性が低いと、例え自分のコードであったとしても時間が経ってから読み返すと「これ、自分が書いたの？」「思い出さない、さっぱり意味分からない」となり時間の浪費に繋がるかもしれません。また、他人の書いたソースコードであれば、なおさらその意図を理解することに時間がかかります。後から直すのも大変です。

👉 最初のコードから創意工夫し綺麗な状態にすることが重要です。バグの発生も抑えられ、例えバグが発生しても簡潔なコードであれば比較的容易に対処できます。将来の自分にとっても、他人にとっても時間の節約と**メンタルケア**に繋がるかもしれません。

さて、下のコードは綺麗といえるでしょうか。さっそく一例をあげます。
_(本文の例は JavaScript っぽく書いていますが、原理原則は言語に問わない内容にするつもりです)_

```js
err = checkError();
while (err) {
  // do something
  err = checkError();
}
```

短いコードで、ぱっと見ると「よくやるパターンではないか」、「簡潔で問題がなさそう」と思われるかもしれません。しかし、実は突っ込みポイントは複数あります。

え、どこでしょう？答えは最後まで読んでいただいたら、文末に開示します。

（注意）コーディングスタイルはの答えは一つだけではありません。これから述べるのはあくまで個人の見解に過ぎなく、ご意見・指摘は大歓迎です。


# では、簡単にソースコードを綺麗にするテクニックを紹介します。

## ポイント１ 実態と名前の違いに気をつける

基本すぎるだろうと思われるかもしれませんが、ロジックの実態と、関数などの名前が乖離しはじめてしまうことがあります（実際に何件かありました）。
ここで言いたいことは、[驚き最小の原則](https://ja.wikipedia.org/wiki/%E9%A9%9A%E3%81%8D%E6%9C%80%E5%B0%8F%E3%81%AE%E5%8E%9F%E5%89%87) に尽きると思いますが、いくつか具体例とともに紹介します。


### 1.1 中身が推測できる関数名をつける

例えば、`getXXX()`や`checkXXX()`のような名前の関数は、読み手に副作用（side-effect）がないイメージを与えます。そのため、`getXXX()` の中で write などの更新が行われると読み手にとって大きな驚きを与えてしまいます。

あるいは、checkXXX()というメソッドが、正常時の返り値として Boolean ではなく、オブジェクトを返すことも、戸惑わせるでしょう。なぜなら、`check`（検査）は OK か NG かを返すのを期待する名前なので、オブジェクト本体を期待するわけではありません。

実態に則した名前をつけるとしたら、`checkAndGet()`となるでしょう。

ただし、そうすると「単一責任原則」（次の「ポイント２ ロジックの複雑度を減す」の説明を参考してください）に違反になるため別々切り分けるほうが良いかもしれません。


場当たり的に対応するのではなく、その関数の関心事を見極めて適切な対応をしましょう。


### 1.2 パラメータや変数名には型や可視性などが推測できる名前をつける

パラメータや変数名は（特にweak-typed言語にといて）値の型まで推測できる名前が望ましいです。

文字列なら`XxxStr`、`xxxName`、数値なら`xxxNum`、`xxxCount`、ブーリアンなら`isXxx`、`hasXxx`、リストなら`xxxList`、`xxxArr`、マップなら`xxxMap`、`xxxDict`などなどよく使われている表現を把握したほうが良いです。

＊【参考】命名規則(wikipedia):https://ja.wikipedia.org/wiki/%E5%91%BD%E5%90%8D%E8%A6%8F%E5%89%87_(%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0)#%E3%83%8F%E3%83%B3%E3%82%AC%E3%83%AA%E3%82%A2%E3%83%B3%E8%A8%98%E6%B3%95

オブジェクト指向言語の`private`や`protected`のアクセス制限仕組みは、その文法がない言語にも応用できることがあります。

例えば、Python などの言語には、private なら`__`(underscore ２個)、protected なら`_`(underscore １個)をつけるルールがあります。もちろんこの場合は、`export _xyz` や `import __utils__`のようなルールに反する定義はやめたほうが良いです。Pythonでは注意喚起のために、あえて違和感のある`from __future__ import xxx`を許していますが、もちろんそれを推奨するわけではありません。

変数名の悪い例を挙げますと、`executedFlag`が`0:NOT_EXECUTED`、`1:EXECUTED`、`-1:EXCEPTION`の取りうるのもよくないでしょう。`flag`は 0/1、true/false の二値のイメージが強いので、本当に3値をとるのであれば`executedStatus`の方がまだ適切でしょう。


### 1.3 実態にあったコメントをつける

ソースを改修した際によくあるのが、コメントの更新を忘れることです。もちろんコメントも同期してアップデートしましょう。

次のコードの改善ポイントは何点あるでしょうか？

```go
// check number positive, if not do nothing
func isPositive(*str) {
    if (*str < 0) {
        *str = 0
        return false
    }
    return true
}
```

## ポイント２ ロジックの複雑度を減らす

コードの複雑度を減らす技術はたくさんあると思いますが、実践しやすいと思われるコツを紹介したいと思います。

### 2.1 関数やモジュールの長さを減らす

言語や目的がそれぞれ違うので、関数やモジュールの長さ制限に明確なルールは存在しません。

ただ、参考にできる観点と原則があります。それは、一つの関数は一つの機能しか担当させないこと（[単一責任原則（Single responsibility principle）](https://en.wikipedia.org/wiki/Single_responsibility_principle)）、言い換えると、コメント一言で説明できるぐらいの量が適切ということです。

ある関数が複数タスクを担当していたとして、ポイント１の「名称には嘘ついていけない」という観点で素直に名称をつけたとすると`checkValidationAndDoTask1AndDoTask2AndSubmit()`的な名前になってしまいます。素直に、タスクごとに関数を分けたほうが分かりやすいでしょう。

１箇所で２つ以上のことをやっていると気づいたら、[関心の分離（Separation of concerns）](https://en.wikipedia.org/wiki/Separation_of_concerns)と言われるように、関数を切り分けるを考慮するべきです。そうすると、それぞれのコードが簡潔になることで結局は理解にかかる時間が元のコードよりも短縮できます。
理由は、人の脳のレジスタの容量が限られているので、一度処理できる情報量はごくわずかです。そして人間はトップダウンの視点からものの全体像を掴もうとします。細かいタスクのやり方（関心）を別箇所に分離できると、脳の負荷を下げることができるからです。

次のコードはあるデータモデルの初期化の例です。
いろいろなステップの実装を展開して、`init`の一箇所に全部書く方法[BEFORE]もできますが、それより、[AFTER]のほうはステップごとに何をやっているのが一目瞭然です。

[BEFORE]

```js
init() {
  // get enviromnment variables
  // do the job ...
  // do the job ...
  // do the job ...
  // prepare default options
  // do the job ...
  // do the job ...
  // do the job ...
  // fetch data
  // do the job ...
  // do the job ...
  // do the job ...
  // check the data's validation
  // do the job ...
  // do the job ...
  // do the job ...
  // transform data into expected form
  // do the job ...
  // do the job ...
  // do the job ...
}
```

[AFTER]

```js
init() {
  get_env()
  prepare_option()
  fetch_data()
  check_data()
  transform_data()
}
```

#### 2.2 ネストを減らす

`if` `else` `while` `switch`などの制御構文が積み重なるほど、インデントが増え複雑度が上がります。
これらの可読性を上げる**定形のコツ**をまとめます。


- 2.2.1 異常系分岐(エラーハンドリング)を先に書き、早期リターンする

[BEFORE] 条件分岐間にどんな依存関係があるのか追うのが難しい

```js
// operation 0
if (check1(something1)) {
  // operation 1
  if (check2(something2)) {
    // operation 2
    if (check3(something3)) {
      // operation 3
    }
  }
}
```

[AFTER] エラーチェックを同じインデントにそろえた方が分かりやすい

```js
// operation 0
if (!check1(something1)) {
  return Exception1;
}
// operation 1
if (!check2(something2)) {
  return Exception2;
}
// operation 2
if (!check3(something3)) {
  return Exception3;
}
// operation 3
```

- 2.2.2 余計な else を省略

else の次は何もやらない場合、else 分岐さえ切る必要ありません。

[BEFORE] else を素直に書いた例

```js
if (condition1) {
  // operation 1
} else {
  // other operations
}
```

[AFTER] elseを無くし、早期リターンする

```js
if (condition1) {
  // operation 1
  return;
}
// other operations
```

また、`if (condition) return`と同じく`if (condition) break` や `if (condition) continue` の後ろの else も省略します。

[BEFORE]

```js
while (condition1) {
  if (hasError) {
    break;
  } else {
    // nomral operations
  }
}
```

[AFTER]

```js
while (condition1) {
  if (hasError) break;
  // nomral operations
}
```

## ポイント３ 書き方の揺らぎを減らす制約ルールを作る

コードを綺麗に書くためは、世にでている規制のコーディング規約を導入するだけではなく、チーム内ルールを作り、各人の揺れを抑えるとお互いのコードが読みやすくなります。
（どこまでやるかの線引は難しいですが、相互にレビューをしていると慣習的なルールが作られることが多いと思いますので、まずそれを明文化しておくことは有益だと思います）

一例として、`a<b`と`b>a`は意味は同じなので、どちらでも書いても問題ありませんが、左は小さいもの、右は大きいものという原則に従い、`<` `<=`を使って、`>` `>=`を避けるという意見をよく耳にします。
`if (a>1 && a<100)`より、`if (1<a && a<100)`（言語によって`if 1<a<100`のような簡略の書き方もある）のほうが視覚的に分かりやすいでしょう。
ただし、単独な`if (42<myNumber)` の使用は「ユーダ記法」([Yoda Condition](https://ja.wikipedia.org/wiki/%E3%83%A8%E3%83%BC%E3%83%80%E8%A8%98%E6%B3%95))になってしまう欠点もあります。Python でユーダ記法で書いたら pylint の慣例違反のワーニング（`misplaced-comparison-constant (C0122)`）が発動されます。

全ての状況で使える正解は無いですが、このようにコードレビューでお互いに異なる指摘をしてしまうのであれば、チームで方針を統一したほうがスムーズでしょう。


## 最後に、文頭に挙げた例について

[BEFORE]

```js
err = checkError();
while (err) {
  // no error, do something
  err = checkError();
}
```

このコードの問題点が分かりましたでしょうか。

まずは、while 条件を見て、`err`が真の時に正常処理のフローに入ることが分かります。
`// err means no error` のコメントをつければ良いでしょうか？
それでもまだ違和感が残りますよね。

その違和感の原因は`checkError`の返り値の内容を関数名から推測できないからです。
今の書き方では以下のような疑問がでてきます。

1. 「check は単純に実行動作を表していて、何も返さないではない？」
1. 「check の対象は Error なので、Error がある時は真を返す？」
1. 「checkError の目的を考え、error のないことを望んでいるはずで、No Error の時は真？」
1. 「Error 発生時、エラーの詳細を返したいので、Error のオブジェクト、あるいは Null を返すかも？」

曖昧過ぎますね。
この`checkError`関数の命名の改善案は３つあげられます。

1. エラー有無を表現する Boolean を返す場合、`hasError`が良いでしょう
2. 1の反対の意味を示したい場合、`hasNoError`、否定形式を気になるなら`isValid`がよく使われています
3. エラー時には詳細のエラーオブジェクトを返し、正常時にはNullを返す場合、`getError`の方が直感的

上記を踏まえると次の用に修正できます。

[MIDDLE] 修正パターン ①

```js
hasErr = hasError();
while (!hasErr) {
  // no error, do something
  hasErr = hasError();
}
```

[MIDDLE] 修正パターン ②

```js
isValid = hasNoError();
while (isValid) {
  // no error, do something
  isValid = hasNoError();
}
```

[MIDDLE] 修正パターン ③

```js
err = getError();
while (!err) {
  // no error, do something
  err = getError();
}
```

さらに、コードの重複も改善できます。これによって最初と最後２箇所同じコードが書かれていることで、メンテで修正した時にどちら片方を編集し忘れてバグが出てしまう不幸をなくせます。
次のコードは重複のコードを１箇所にまとめました。

[AFTER] 修正パターン１ (他パターンは同様なので省略します。)

```js
while (!hasError()) {
  // no error, do something
}
```

エラーオブジェクトの中身を取り出して、追加処理したい場合には次のようにも書けますね。

[FINAL] 修正パターン３

```js
while (true) {
  err = getError();
  if (err) {
    handle(err);
    break; // 場合によって、エラーを解消したらcontinueしたいケースも対応できる
  }
  // no error, do something
}
```

## ポイントまとめ

- コードを綺麗にするのは自分と他人の**時間の節約**に繋がります
- ソースコードを綺麗にするための３点
  - 嘘をついてはいけない
  - ロジックの複雑度を減らす
  - 書き方の揺らぎを減らす制約ルールを作る

コーディングはキャンプに行くことと似ている点があると思います。キャンプ場にゴミがあったら、誰が捨てたかに関係なく全て持って帰りますよね？

チームのリポジトリに対しても同様に、チェックインする時にチェックアウトした時より少しでも綺麗にするようにしたら、確実に素晴らしいプロジェクトになるのではないでしょうか。
