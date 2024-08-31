---
title: "Zodでyupのwhen/is/thenを表現するには？"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## これは何？

yupからzodへの書き換えを行っていたのですが、その際、yupにてis/when/thenを用いたスキーマがありました。
これは「Aフィールドがhogeの時は、B,C,Dフィールドが存在する。AフィールドがfugaのときはC,D,Eフィールドが存在する...」といったような
あるUnion値を取るフィールドの変化によって他のフィールドの状況が変化するようなスキーマです。

