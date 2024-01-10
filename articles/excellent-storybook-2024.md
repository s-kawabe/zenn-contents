---
title: "詳解 Storybook"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "typescript", "storybook", "designsystem"]
published: false
---

こんにちは、都内でフロントエンドエンジニアをしている [@sh1ntaro_dev](https://twitter.com/sh1ntaro_dev) です。
みなさんは普段のフロントエンド開発で Storybook は使っていますでしょうか？
チームでのプロダクト開発において、生産性を向上させるようなツールや技術は様々あると思いますが、個人的にはそのような技術の中でも特に Storybook は導入において非常に多くのメリットがあると考えています。

この記事では基本的なものから応用的なものまで Storybook を使ってできることについて紹介していこうと思います。

また、今回は React でのユースケースを前提として記載をしていますのでご了承ください。

# 1. Storybook とは

## 機能



## 導入するメリット

## Story を書いてみる

## CSF2.0 と CSF3.0

# 2. 基本的な使い方

## アプリケーションのコンポーネントカタログとして使う

基本の使い方はコンポーネント毎に Story を作成してローカルで `yarn storybook` などとすることで Storybook を立ち上げられるような環境にしておくことです。
実際導入しているチームでも、「明確な運用ルールは無いが、とりあえずアプリケーションに使うコンポーネントごとに Story を作成してエンジニアのみがローカルで見れるようにしてある」
というところも、なんだかんだ多いのではないでしょうか。

個人的にはメリットの章にも書いた通り、それだけでもかなりの効果があると思っています。

基本的な Button コンポーネントを例にして React コードに対する Story ファイルの記述を見てみましょう。

``` Button.tsx
```

``` Button.stories.tsx
```

一番簡単な例として、props を用いて `文字列やイベントハンドラを受け取って表示に徹するコンポーネント` を挙げましたが
もっと複合的な Molecules または Organisms 相当のコンポーネントに対する Story の場合は、その中身に state やその他ロジックが依存している可能性が高いです。
このような時の Story の作り方には多少工夫が必要になるでしょう。

## Storybook を静的URLにホスティングする

main ブランチ最新のコードベース状況の Storybook を静的URLにホスティングすることで、最新のコンポーネントカタログを参照することができます。
これによって各エンジニア自身だったり、適宜 PdM や Designer などと議論しながら開発を進めるののに非常に役に立ちます。

https://storybook.js.org/docs/sharing/publish-storybook

公式サイトにもこれについての実現方法が記載されており、[Chromatic](https://www.chromatic.com/) を使う方法と GitHub Pages にデプロイする方法が紹介されています。
Chromatic は Storybook の開発チームが運用しており、ホスティングやビジュアルテストなど様々な機能を提供してくれるサービスです。

## よく使う addon の紹介

Storybook には addon と呼ばれる、Story を参照する際に追加機能を提供してくれるような仕組みがあります。
これを利用することで例えば,
- props をインタラクティブに変更してUIの変化を確認する
- viewpoart をデザインシステム要件に沿って変えて確認する
- a11y 要件を満たしているかコンポーネントごとに確認する
- ...etc
など普段の使い方に加えてより生産性を上げるための体験をプラスすることができます。
package.json になんちゃらaddonみたいな名前がいっぱいあるけど、正直どれがどれだか理解していない... という方も多いのではないでしょうか。

ここではよく使われている addon についての紹介を少し行います。

# 3. 応用的な使い方

## デザインシステムのドキュメントとして使う

https://storybook.js.org/tutorials/design-systems-for-developers/react/en/introduction/

公式サイトにもこのようなチュートリアルの章として記載がありました。
今までの説明でコンポーネントごとにカタログを生成し、 「状態や引数の変化による様々な見た目の振る舞いをチェックできる」ことがお分かりいただけたかと思いますが
このような機能は、まさにデザインシステムの構築においてとても役に立ちそうです。

Storybook は [MDX](https://storybook.js.org/docs/writing-docs/mdx) での記法もサポートしており、これを用いることで
コンポーネントごとのカタログに加え詳細な文書を残したりすることも可能です。

デザインシステムを公開している企業において、そのデザインシステムの原則や Philosophy を公開すると同時に、運用している Storybook についても public に公開しているところがあります。
以下はそのいくつかの例です。
- [Spindle](https://ameba-spindle.web.app/?path=/story/bottombutton--fixed-position) (CyberAgent)
- [SmartHR UI](https://396b729--63d0ccabb5d2dd29825524ab.chromatic.com/?path=/story/buttons%EF%BC%88%E3%83%9C%E3%82%BF%E3%83%B3%EF%BC%89-button--button) (SmartHR)
- [Spectrum](https://opensource.adobe.com/spectrum-web-components/storybook/index.html?path=/story/accordion-densities-compact--s) (Adobe)

また、補足ですが以下の記事では、デザインシステムの Storybook を見据えた運用についてコードレビューやテスト、デプロイ周りを絡めたプラクティスについて紹介されています。
デザインシステム開発というと、かなりの確率で Storybook という単語が登場するほど親和性の高い技術であることがわかると思います。

https://www.chromatic.com/blog/why-design-systems-are-a-single-point-of-failure/

## テストに使用する

続いて、Story ファイルはテストにも利用することができます。
特に上述した CSF3.0 からはテストへの Story ファイルの応用が幅広くなり、フロントエンドの開発者体験を大きく向上させました。

### Integration Test (Interaction Test)

[Testing Trophy](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications) における Integration の層にあたるテストです。
例えば、登録フォームのような幾つかのフィールドからなる、Input のような Atom コンポーネントを組み合わせてできた複合コンポーネントがあるとします。
これにはしばしばユーザーの一連の行動（文字や数値をタイプしてバリデーションを通り抜けて Submit ボタンを押す）をまるっとテストしたいケースが多いと思います。

このような時、Storybook の Play function や composeStories といった API が役に立ちます。

### Accesibillity Test

### Vusual Regression Test

## Pull Request 毎に Storybook をデプロイする

main ブランチを反映した静的 URL 以外にも、Pull Request ごとにその変更を反映した URL が生成される仕組みがあると、コードレビューが捗ってより良い開発体験になると思います。
（実際、コーレドレビューをしていて、Hoge.tsx の変更差分が出てきた時に「これどんな見た目だっけ？」となりませんか..？）

ただこの方法は CI/CD の実行時間を増やしたり、デプロイ回数が増えたりすることにつながるのでコストと一緒に検討することが重要だと思います。

方法としては、以下のような GitHub Actions を書いて Chromatic や GitHub Pages など任意のサイトにホスティングをすることになるはずです。
以下は Pull Request ごとに Netlify へデプロイを行う例です。（正直あまり詳しくないので ChatGPT 産コードです。）

```yaml:storybook.yaml
name: Deploy Storybook

on:
  pull_request:
    paths:
      - 'src/**/*.tsx'

jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Install dependencies
      run: npm install
    - name: Build Storybook
      run: npm run build-storybook
    - name: Deploy to Netlify
      uses: netlify/actions/cli@master
      env:
        NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
        NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
      run: netlify deploy --dir=storybook-static --prod
```


## Storybook をプライベートにする

また `Storybook を静的URLにホスティングする` の章では記述しなかったですが、企業でのプロダクト開発ではコンポーネントカタログと言えど public な URL で誰でもみれてしまう環境というのは少し不安があると思います。
この要件についても、Chromatic を導入することで比較的楽に実現が可能なのですが、Amazon S3 や Cloud Storage のようなサービスでも IAM の設定次第で実現可能だと思います。
他のサービスで言うと、GitHub Pages にはサイトをプライベートに保護する機能は存在しておらず、firebase hosting や Netlify においては Pro プランのようなものでないと実現できないようでした。

S3 の例では以下の記事が参考になりました。
https://zenn.dev/cumet04/articles/private-storybook-on-pullreq

# Q & A

以上で Storybook を使ってできることについて紹介してきましたが、実際に運用していくにあたり生まれがちな疑問について
個人的な見解ではあるものの、いくつかパターンを記録しておきたいと思います。

## コンポーネントがAPI通信に依存している場合はどうしたらいい？

- [loaders](https://storybook.js.org/docs/writing-stories/loaders) という機能を使う
- msw のようなAPI Mockライブラリを使用する
- コンポーネント設計自体をContainer / Presenter パターン ([参考](https://zenn.dev/buyselltech/articles/9460c75b7cd8d1)) のような構造にする


## Story を作成する粒度はどうする？

どの粒度に Story を作成するかという疑問が生まれると思います。

# さいごに

Storybook 最高！