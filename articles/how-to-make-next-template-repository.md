---
title: "Next.jsのテンプレートリポジトリをつくろう"
emoji: "⏭️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "eslint", "prettier", "typescript"]
published: false
---

# はじめに
Webフロントエンジニア歴5ヶ月の初心者です。
最近のおしゃれなエンジニアは、あらゆるエコシステムの素振りや、新しいプロジェクトの雛形として自分なりのテンプレートリポジトリを作っているようなので私も真似してみたく、
この度これの作成過程を記録しておこうと記事にしてみました。

拙い点や改善点がありましたら、ご指摘いただけると大変嬉しいです🙇‍♂️
特に各技術の言語化などに関しては自信のない部分があります...

また、今回の完成版リポジトリに関して(動作は保証できませんが...)自由にご使用いただいて構いません！

**2021/12/11時点の完成形**


# 対象読者
- フロントエンド開発をする方
- Next.jsで何かを作る時に毎回0からベースを作るのがしんどい方
- 開発に役立つ便利ツールは入れたいが、全ていちいち調べるのが面倒な方

# 作成環境
今回はyarnを使用していきます。
またそれぞれのバージョンは以下です。
pcはm1macbook proを使用しています。

| 対象 | version |
| ---- | ---- |
| node | 16.6.0 |
| yarn | 1.22.17 |
| npx | 7.19.1 |

# 0. リポジトリの概要
## ディレクトリ構成
https://zenn.dev/yoshiko/articles/99f8047555f700

こちらの記事が非常に魅力的でしたので参考にさせていただきました。
アプリケーションによってしまうところはあるかと思いますが、個人的に技術を試す時などもディレクトリで
迷いたくないので自分の中でのデファクトスタンダード的なものを根付かせたいと思い
テンプレート段階でディレクトリ構成にも関与することにしました。

## 含めるもの
方針としては、**汎用性の高いテンプレートリポジトリ**としたいのでアプリケーションの土台としても使えるし、
ちょっとした試したいライブラリの環境としても使えるようなものを想定しています。
その為、最近Storybookを使っていたり、Next.jsでSSRするならばnext-seoのようなものも欲しいところですが
ここら辺は一旦含めておりません。
またスタイリングに関してもTailwindを使ったりChakraUIを使ったりバラバラなのでこれも含めておりません。

### Next.js
そもそもなぜ素のReactでは無いかについては、
最近はSSRの必要が無い場合でもNext.jsを使うことが多かったり、さくっとライブラリを試す時などは
ルーティングが楽でコードの記述量が少なくて良いのでテンプレートとしては最初からNextを入れておくことにしました。
(しかし最近のreact-router v6やReact Locationなどによるライブラリにより、フレームワークに依存しないpureなreact構成も良さそうかな〜と感じはじめております。)

### Typescript
生きていく上で必須。

### eslint
ここまで書いてきましたが、個人的なテンプレートリポジトリを作成したい理由としては
ほぼeslintの設定を毎回どこかからコピーして見直して、、みたいなのが面倒なためでした。
eslintを入れない理由もおそらく無いのでこちらも含めておきます。

### prettier
prettierに関してもテンプレートをcloneした時点で最初から保存時に整形が効いてほしい為
入れておくことにしました。
一緒に`vscode/settings.json`などもコミットしておきます。

### husky
commit時にlinterを走らせてくれるものです。
「eslintによってエディタ上でlinterエラーは分かるのでは？」
と思われるかもしれないのですが、コード変更によって、予定外のファイルがそれに依存していて
エラーに気づかないようなケースもある為入れておきたいなと思いました。

### jest, react-testing-library
正直なところ、今までフロントエンドのテストを積極的に学んでこなかったので宇賀
これからのプロジェクトや個人開発ではちゃんとやっていこう！という思いから、組み込んでおくことにしました。

### scaffdog
[scaffdog](https://github.com/cats-oss/scaffdog)はmarkdownを書くことにより、
コンポーネントのテンプレートを簡単に作成できるライブラリです。
最近仕事でも使用させていただいており、非常に使い心地がよいので入れておくことにしました。
これにより、作成するコンポーネントごとの微妙な構文の違い(VFCつけるつけない、propsをDestructuringするしない)を
統一できたりしてちょっと開発スピードが上がりそうです。

### ErrorBoundary
[ErrorBoundary](https://ja.reactjs.org/docs/error-boundaries.html)とは、Reactコンポーネントツリー内で発生したエラーを
ハンドリングし切れない場合、クラッシュしたアプリケーションの代わりのコンポーネントを表示させる機構です。
https://zenn.dev/azukiazusa/articles/60933e9cb1a4bc
こちらの記事を参考にさせていただき、
念の為入れておくことにしました。
React Server Componentsの登場や、Suspenseとの併用なども後々考えていきたいなと思います。
（ここらへんはちょっと勉強中の為、ツッコミどころあるかと思いますが...）

### renovate
[renovate](https://github.com/renovatebot/renovate)はリポジトリ内のライブラリアップデートを自動化してくれるものです。
さまざまなカスタマイズが可能で、botがpackage.jsonとlockファイルの更新PRを自動で出してくれます。
設定のよっては、PRを勝手にマージさせるということもできます。

似たようなツールとしてはdependabotがあるかと思うのですが、正直今回はどちらでも良いかなという印象でした。
しかし知り合いの方にrenovateを教えてくれる人がいたので今回はこちらを採用しています。

以下を参考にさせていただきました。
https://qiita.com/takiga/items/6ec7b9c9613ec8bf7d51

---

# 1. リポジトリの作成
GitHubにリポジトリを作成します。
今回名前は「**next-ts-template**」としました。

# 2. Next.jsとTypeScriptの設定

## install
最初にNext.jsで環境を作ります。
TypeScriptには[examples](https://nextjs.org/examples)という、さまざまなライブラリと一緒に使う際の
参考リポジトリのようなものがあるのですが、こちらに関しては所々バージョンが低かったりする印象を受けたことと
色々自分で入れてみることで勉強にもなるかなと思ったので今回は使っていません。

[公式サイト](https://nextjs.org/docs/api-reference/create-next-app)にある通り、
typescript標準装備な環境構築コマンドを実行します。

```
npx create-next-app@latest next-ts-template --ts
```

「Need to install the following packages:
  create-next-app@latest」
と言われた場合はyを入力しEnterを押します。

## 確認
プロジェクトのルートディレクトリで以下を実行します。

```
yarn dev
```

localhost:3000にアクセスし以下が表示されればOKです。



# 3. eslintとprettierの設定

# 4. huskyの設定

# 5. jest, react-testing-libraryの設定

# 6. scaffdogの設定

# 7. ErrorBoundaryの設定

# 8. renovateの設定

# さいごに

# 参考にさせていただいたもの
- https://zenn.dev/yoshiko/articles/99f8047555f700
- https://github.com/cats-oss/scaffdog
- https://ja.reactjs.org/docs/error-boundaries.html
- https://zenn.dev/azukiazusa/articles/60933e9cb1a4bc
- https://zenn.dev/berlysia/articles/5dfa58f282aa14
- https://qiita.com/takiga/items/6ec7b9c9613ec8bf7d51
- https://mo-gu-mo-gu.com/create-next-app-typescript/
- https://nalog.work/minh8zakv