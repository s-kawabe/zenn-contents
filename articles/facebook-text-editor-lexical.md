---
title: "新テキストエディタライブラリ lexicalとは"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript", "react", "lexical", "draftjs"]
published: false
---

::: message
2022年4月時点 ver0.2.4 での内容になるため
今後のアップデートによっては以下の内容が変更となっている可能性があります。
:::
# はじめに
https://github.com/facebook/lexical

lexicalというテキストエディタフレームワークが先日OSS化され、少し調べてみたのでその仮定と実装を記録しておきたいと思います。

このライブラリの開発元であるMetaが過去に出している[Draft.js](https://github.com/facebook/draft-js)を業務で使用しているのですが
運用が辛く、謎のエラー見舞われていたりもするため、置き換えを検討していたところにこの発表があったので今回触ってみることにしました。

また今回の内容に不適切な内容がありましたらその点ご指摘いただけると幸いです。

::: message
Meta社内でもDraft.jsは今後運用・保守が廃止されている様子のようです。
https://github.com/facebook/draft-js/issues/3091

> Q. 前回のリリースから1年以上経ちましたが、今後もDraft.jsの開発は継続されますか？

> A. 事実上保守されておらず、Draft.jsインスタンスは全てlexicalに移行しています。
:::

また、今回の実装はNext.jsで素振りしておりコードと動作デモは以下にあります。
(公式のCodeSandBoxとほぼ同じですが...)

GitHub
https://github.com/s-kawabe/the-best-text-editor

デモ


# lexical とは


# Draft.jsとの比較

## ライブラリサイズと依存パッケージ

- lexical
https://bundlephobia.com/package/draft-js@0.11.7
- draft-js
https://bundlephobia.com/package/lexical@0.2.4

パッケージサイズがlexicalの方がかなり小さくなっていることがわかる。
またDraft.jsは[immutable.js](https://immutable-js.com/)に依存しているが、lexicalは何のパッケージにも依存していない。
(Draft.jsのimmutable依存はなかなかに扱いづらい点があったので個人的には嬉しい修正です。)

# Lexicalの設計/データ構造
本記事では簡単にご説明します。
詳しく説明されている記事があったためより詳しく知りたい方はそちらの記事や、公式ドキュメントをご参考ください

## RichTextEditor/PlaneTextEditor
 Lexicalではエディターを管理する方法が明確に2つのAPIに分かれています。
 名前でだいたい察しがつきそうなのですが、前者がさまざまなスタイルをカスタマイズできるもので
 後者がプレーンなテキスト(とは言ってもcontentEditableをtrueにしてはいる)のみで表現を行う形かと思います。

## Nodes
https://lexical.dev/docs/concepts/nodes

## Commands
https://lexical.dev/docs/concepts/commands

## Listeners
https://lexical.dev/docs/concepts/listeners

## Selection
https://lexical.dev/docs/concepts/selection

## EditorState
https://lexical.dev/docs/concepts/editor-state


# Plugin
https://lexical.dev/docs/react/plugins




# 参考にさせていただいたもの
https://lexical.dev/docs/intro
https://zenn.dev/haruru/articles/12206977dc0c48
https://zenn.dev/stin/articles/getting-started-with-lexical
https://codesandbox.io/s/lexical-rich-text-example-forked-jg4cy5 (RichTextEditorの例)
https://codesandbox.io/s/lexical-plain-text-example-g932e (PlaneTextEditorの例)