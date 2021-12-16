---
title: "Next.jsのテンプレートリポジトリをつくろう"
emoji: "⏭️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "eslint", "prettier", "typescript"]
published: true
---

# はじめに
Webフロントエンジニアになって5ヶ月目の者です。
最近のおしゃれなエンジニアはあらゆるエコシステムの素振りや、新しいプロジェクトの雛形として自分なりのテンプレートリポジトリを作っているようなので私も真似してみたいなと思い、
この度これの作成過程を記録しておこうと記事にしてみました。

拙い点や改善点がありましたら、ご指摘いただけると大変嬉しいです🙇‍♂️
特に各技術の言語化やeslintの設定ファイルの書き方などに関しては自信のない部分があります...

また、今回の完成版リポジトリに関しては(動作は保証できませんが...)自由にご使用いただいて構いません！

**完成形リポジトリ(※2021/12/14記述時点からアップデートされている可能性があります)**
https://github.com/s-kawabe/next-ts-template

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
| macOS | BigSur 11.5.2 |
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

### husky, lint-staged
commit時にlinterやformatterのチェックを走らせてくれるものです。
「eslintによってエディタ上でlinterエラーは分かるのでは？」
と思われるかもしれないのですが、コード変更によって、予定外のファイルがそれに依存していて
エラーに気づかないようなケースもある為入れておきたいなと思いました。
また、エラーがあった場合CIなどでチェックせず、コミット前に検知できるのも魅力的です。

### jest, react-testing-library
正直なところ、今までフロントエンドのテストを積極的に学んでこなかったのですが、
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
エラーに関しては念の為入れておくことにしました。
React Server Componentsの登場や、Suspenseとの併用なども後々考えていきたいなと思います。
（ここらへんはちょっと勉強中の為、ツッコミどころあるかと思いますが...）

### renovate
[renovate](https://github.com/renovatebot/renovate)はリポジトリ内のライブラリアップデートを自動化してくれるものです。
さまざまなカスタマイズが可能で、botがpackage.jsonとlockファイルの更新PRを自動で出してくれます。
設定によっては、PRを勝手にマージさせるということもできます。

似たようなツールとしてはdependabotがあるかと思うのですが、正直今回はどちらでも良いかなという印象でした。
しかし知り合いの方にrenovateを教えてくれる人がいたので今回はこちらを採用しています。

以下を参考にさせていただきました。
https://qiita.com/takiga/items/6ec7b9c9613ec8bf7d51

### nprogress (2021/12/15 追記)
zennなどでも使用されている、ページ遷移時に画面の上を青い線が走るUIを実装できます。
実装は簡単なもののUX向上としてかなり良いツールだと思い入れることにしました。

以下を参考にさせていただきました。
https://zenn.dev/yusugomori/articles/51e63a4aa9dc27e21124

---

# 1. リポジトリの作成
GitHubにリポジトリを作成します。
今回名前は「**next-ts-template**」としました。

# 2. Next.jsとTypeScriptの設定

## packageインストール
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
![](/images/how-to-make-next-template-repository/success-yarn-dev.png)

## ディレクトリ変更
Next.jsに関しては環境構築直後はsrcディレクトリが無いのですが、最近は「page component以外のcomponentやそれに関わるファイル」というのは
(publicなど、Next.jsのルール上ルートに配置する制約があるもの以外は)ほとんどsrcに纏めるのが主流だと思うので変更していきます。
また、概要で参考にさせていただいたもので大体のディレクトリ構成も決めてしまいます。

以下のような状態になりました。

**before**
```
next-ts-template
├── pages
│   ├── _app.tsx
│   ├── api
│   └── index.tsx
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── styles
│   ├── Home.module.css
│   └── globals.css
...
```

**after**
```diff
next-ts-template
├ src
│ ├── components
│ │   ├── layout // レイアウトに関するもの
│ │   ├── model // userやpostなど特定のモデルに関するもの
│ │   ├── page // ページコンポーネントそのままのもの(pagesの方でこの中のものをimportする)
│ │   └── shared // 特定のモデルに依存しない共通のui
│ ├── hooks // hooksをまとめる
│ ├── pages // Next.jsからページとして認識される場所(必須)
│ └── utils // 共通関数などをまとめる
├ test // testをまとめる
├ public // 画像などの静的ファイルを入れる
...
```

test用のディレクトリとpublicのみsrc外に出して、それ以外はsrc内に含めてしまいます。
将来的には[このような](https://www.npmjs.com/package/eslint-plugin-strict-dependencies)eslintルールを用いて、
ディレクトリ間の依存関係ルールも決めていきたいなと思います。
また、まだ実際に試していない為今後こういった部分は試行錯誤していくところかなと思います。

## tsconfig.jsonの変更
ファイルをimportする際、絶対パス指定で固定したいのと、srcファイル配下のものは「@」をつけて
わかりやすくしたいので`tsconfig.json`のcompilerOptions内に`baseUrl`と`paths`を設定します。

```diff json:tsconfig.json
{
  "compilerOptions": {
    ...
+   "baseUrl": ".",
+   "paths": {
+     "@/*": ["src/*"],
+     "~/*": ["./*"],
+   },
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

:::message
こちらのエイリアス設定はStorybookなど、ライブラリによってはパスを解決してくれない場合があり追加設定が必要なことがあります。
また、create-react-appなどではwebpackの設定変更が必要になります。
:::

これによって各ファイルのimport部分が次のようになり、とても見やすくなることが想定されます。

```tsx
// node_modulesから読み込んだもの
import { useRouter } from 'next/router'
import type { VFC } from 'react'
// 内部のsrc配下から読み込んだもの
import { someComponent } from '@/components/some'
// 内部のルートディレクトリから読み込んだもの
import { someSetting } from '~/someSetting'
```

# 3. eslint, prettierの設定

## packageインストール
以下のコマンドでパッケージをインストールします。

typescript系
```
yarn add -D @typescript-eslint/eslint-plugin @typescript-eslint/parser
```
prettier系
```
yarn add -D prettier eslint-config-prettier
```
その他便利系
```
yarn add -D eslint-plugin-import eslint-plugin-simple-import-sort
```

## eslint設定ファイル変更

Next.jsのversion11からはデフォルトで`eslint-config-next`というものが入っており、こちらには`react`, `react-hooks`, `a11y`などの基本的なルールは既に入っている状態になっているようです。 ([参考](https://zenn.dev/thiragi/articles/555a644b35ebc1))

個人的に中のルールに対してコメントを書いておかないと忘れてしまう為、ファイル名を`eslintrc.json`から`eslintrc.js`に変え、中身を以下のようにmodule.exportsを追加します。

```diff js:.eslintrc.js
+ module.exports = {
  // いろんなルール
}
```

このようにした上で、中身を以下のようにいろいろと追加します。

```js:.eslintrc.js
module.exports = {
  root: true,
  env: {
    browser: true,
    es6: true,
    node: true,
  }, 
  parser: "@typescript-eslint/parser",
  parserOptions: { 
    project: "./tsconfig.json", 
    ecmaFeatures: {
      jsx: true,
    },
    sourceType: 'module',
  },
  plugins: ['react-hooks', 'react', '@typescript-eslint', 'import', "simple-import-sort"],
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "next",
    "next/core-web-vitals",
    "prettier",
  ],
  rules: {
    "no-console": ["warn", { allow: ["warn", "info", "error"] }], // console.logが残っていればwarn
    "no-restricted-syntax": [ // for in, for of, enumは使ってはいけない
      "error",
      {
        selector: 'ForInStatement',
        message: 'for..in loops iterate over the entire prototype chain, which is virtually never what you want. Use Object.{keys,values,entries}, and iterate over the resulting array.',
      },
      {
        selector: 'ForOfStatement',
        message: 'iterators/generators require regenerator-runtime, which is too heavyweight for this guide to allow them. Separately, loops should be avoided in favor of array iterations.',
      },
      { 
        selector: "TSEnumDeclaration", 
        message: "Don't declare enums" 
      }
    ],
    "prefer-arrow-callback": "error", // arrow functionを許可
    "prefer-const": "error", // const推奨
    "func-style": ["error", "expression"], // 関数式を使わなければいけない
    "arrow-body-style": ["error", "always"], // 関数式の中身は必ず中括弧で囲む
    "no-restricted-imports": ["error", { paths: [{ name: "react", importNames: ["default"] }] }], // reactの明示的なimportは不要なので禁止
    "react/prop-types": "off", // ts使うので不要
    "react/react-in-jsx-scope": "off", // reactはグローバルなので不要
    "react/display-name": "error", // 無名関数を禁止する
    "react/no-unused-prop-types": "error", // 未使用propsはエラー
    "react-hooks/rules-of-hooks": "error", // hooksの基本的なlinter
    "react-hooks/exhaustive-deps": "warn", // effectやcallbackのdeps linter
    "import/newline-after-import": "error",
    "import/no-default-export": "error", // default-exportを禁止する
    "simple-import-sort/imports": "error", // import文の整列
    "simple-import-sort/exports": "error", // export文の整列
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/explicit-module-boundary-types": "error",
    "@typescript-eslint/consistent-type-imports": ["warn", { prefer: "type-imports" }],
    "@typescript-eslint/no-unused-vars": ["error", { varsIgnorePattern: "^_", argsIgnorePattern: "^_", }], // 未使用変数はエラー
  },
  overrides: [ // 一部ルールを除外する
    {
      files: ["src/pages/**/*.tsx"], // pagesのdefault exportは仕方ないので除外
      rules: { "import/no-default-export": "off" },
    },
    {
      files: ["**/*.tsx"], // componentの戻り値の型定義の記述は必須にしない
      rules: {
        "@typescript-eslint/explicit-module-boundary-types": "off"
    }
  }],
}
```

今回中身については触れません、また自分も調べて切れていないためよくないところがあるかもしれません...
`.eslintignore`の中は以下のようにしました。

```
**/node_modules/*
**/out/*
**/.next/*
package.json
*.config.js
.eslintrc.js
renovate.json // (あとで追加するファイル)
```

## prettier設定ファイル変更

prettierの設定は好みになると思いますが、個人的には`package.json`に以下のように書き加えてしまいます。

```diff json:package.json
{
  ...
+  "prettier": {
+    "trailingComma": "es5",
+    "semi": false,
+    "singleQuote": true,
+    "printWidth": 120
+  },
  ...
}
```

## vscode設定変更

こちらも好みになるところがあると思いますが
自分は保存時にprettierで整形されるように`.vscode/settings.json`ファイルを作成し中身を以下のようにしました。

```json:settings.json
{
  "eslint.workingDirectories": [ { "mode": "auto" } ],
  "editor.formatOnSave": false,
  "[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode", "editor.formatOnSave": true },
  "[typescriptreact]": { "editor.defaultFormatter": "esbenp.prettier-vscode", "editor.formatOnSave": true },
  "editor.codeActionsOnSave": { "source.fixAll.eslint": true },
  "eslint.validate": [
    "json",
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
}
```

## vscode拡張機能追加
vscodeの拡張機能の検索でそれぞれ「eslint」「prettier」と検索して
一番上に出てくるものをインストールしておきましょう。

## scripts追加、動作確認

最後に`package.json`のscriptsに以下を追加します。

```diff json:package.json
{
"scripts": {
    ...
+   "lint:eslint": "eslint --cache .",
+   "lint:prettier": "prettier --check .",
  }
}
```

また、`eslintrc.js`の構文誤りなどを確かめるために一度上に追加したscriptsを動かして試しておきましょう。
```
yarn lint:eslint
yarn lint:prettier
```

scripts実行の結果、構文エラーなどで落ちていなければよいです。
また、適当なコード内で適当なスペースを入れたりしてみて、保存時にprettierが動いてくれていればOKです。

（eslintとprettierの設定に関しては[こちら](https://github.com/lightsound/nexst)のリポジトリをとても参考にさせていただきました。）

# 4. husky, lint-stagedの設定

## packageインストール
以下のコマンドでパッケージをインストールします。

```
yarn add -D husky lint-staged
```

## 設定ファイル変更
`package.json`に以下を追加します。

```diff json:package.json
{
  ...
+  "lint-staged": {
+    "*.{js,jsx,ts,tsx}": [
+      "eslint --fix",
+      "prettier --write"
+    ]
+  }
  ...
}
```

`npx husky install`を実行し、husky用のディレクトリを生成します。
```
❯ npx husky install
husky - Git hooks installed
```

`package.json`のscriptsに以下を追加します。
```diff json:package.json
{
  ...
+  "scripts": {
+    "prepare": "husky install"
+  }
  ...
}
```

`prepare`とは[git hooks](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA-Git-%E3%83%95%E3%83%83%E3%82%AF)の機構においてgitからリポジトリをcloneした時点に設定を有効化するもののようです。
yarn2をお使いの場合は`prepare`が動作しないようなので[こちら](https://fwywd.com/tech/husky-setup)の記事などを参考にすると良いかと思います。

そして`npx husky add`コマンドを実行し`pre-commit`ファイルという、コミット前に実行したいコマンドを記述するファイルの生成を行います。
```
npx husky add .husky/pre-commit "npx lint-staged"
```

`.husky/pre-commit`ファイル内を確認しておきましょう
他にcommit時、push時に追加したい処理があればこちらに追記します。
```shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

## 動作確認

適当にコンポーネントファイルを編集し、ターミナルから`git commit`してみます。

```
✔ Preparing...
✔ Running tasks...
✔ Applying modifications...
✔ Cleaning up...
[main 3849f8b] husky test
 2 files changed, 1 insertion(+), 2 deletions(-)
```

このように正常にlintチェックが走ってくれました！

# 5. jest, react-testing-libraryの設定
(ここら辺も恥ずかしながら初心者の為、不適切な点はご指摘いただけるとありがたいです🙇‍♂️)
テストについては勉強不足のためほぼ[こちら](https://github.com/lightsound/nexst)のリポジトリを参考にさせていただきました。
また、一旦ここではテストの確認を行いません。(後日追記していきたいなーと思います。。)
## packageインストール
以下のコマンドでパッケージをインストールします。

```
yarn add -D jest @types/jest ts-jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

## 設定ファイル変更

`package.json`にtest用のscriptsを追加します。
```diff json:package.json
{
  ...
  scripts: {
+    "test": "jest"
  }
  ...
}
```

ルートディレクトリに以下の2つのファイルを作成し、それぞれを編集していきます。
- `jest.setup.js`
- `jest.config.js`

```js:jest.setup.js
import '@testing-library/jest-dom/extend-expect'
```

```js:jest.config.js
const nextJest = require("next/jest");

const createJestConfig = nextJest({ dir: "./" });

/**
 * @type {import('@jest/types').Config.InitialOptions}
 **/
const customJestConfig = {
  setupFilesAfterEnv: ["<rootDir>/jest.setup.js"],
  modulePathIgnorePatterns: ["<rootDir>/e2e/"],
  moduleNameMapper: { "src/(.*)": "<rootDir>/src/$1" }, 
};

module.exports = createJestConfig(customJestConfig);
```

`setup.js`には、jest-domというDOM要素のテストのためにJestを拡張するライブラリをimportしておきます。
こちらはReactTestingLibraryを使用するために必須ではないのですが、テストの作成がより便利になるそうです。

`config.js`にはjestの設定の基礎となるものを記述しています。

# 6. scaffdogの設定
## packageインストール
以下のコマンドでパッケージをインストールします。

```
yarn add -D scaffdog
```

## 設定ファイル変更

`package.json`に以下のscriptsを追加します。
```diff json:package.json
{
  ...
  scripts: {
+    "component": "scaffdog generate component"
  }
  ...
}
```

そしてルートディレクトリに`.scaffdog/`ディレクトリを作成し、以下の2つのファイルを含めます。

```js:config.js
module.exports = {
  files: ['./*'],
};
```

```md:component.md
---
name: "component"
root: "."
output: "**/*"
ignore: []
questions:
  name: "Please enter component name."
---

# `{{ inputs.name | pascal }}.tsx`

```typescript
import type { VFC } from 'react'

type {{ inputs.name | pascal -}}ViewProps = {
} & {{ inputs.name | pascal -}}Props

const {{ inputs.name | pascal -}}View: VFC<{{ inputs.name | pascal -}}ViewProps> = (props) => {
  return null
}

type {{ inputs.name | pascal -}}Props = {
}

export const {{ inputs.name | pascal }}: VFC<{{ inputs.name | pascal -}}Props> = (props) => {
  return <{{ inputs.name | pascal -}}View {...props} />
}
```

コンポーネントに関しては、最近ビューとロジックを分けたい感じがあり、このような構造にすることが多く
雛形としてこのようにしておきます。
Storybookを使用するプロジェクトでは、こちらのmarkdownに追加でstoryファイルの
設定も記述したり、テストファイルも記述したりすると良さそうです。

## 動作確認
`yarn component`を実行すると、作成するコンポーネントを聞かれますので入力すると
作成するディレクトリを選択することができます。
ディレクトリを選択すると以下のように、markdownの記述と入力されたコンポーネント名に沿って
新しいファイルを自動で生成してくれます。

```
? Please enter component name. Hoge
🐶 Generated 1 file!
✔ src/components/Hoge.tsx
✨  Done in 3.57s.
```

👇生成されたコンポーネント
```tsx: Hoge.tsx
import type { VFC } from 'react'

type HogeViewProps = {} & HogeProps

const HogeView: VFC<HogeViewProps> = (props) => {
  return null
}

type HogeProps = {}

export const Hoge: VFC<HogeProps> = (props) => {
  return <HogeView {...props} />
}
```

# 7. ErrorBoundaryの設定
## packageインストール
以下のコマンドでパッケージをインストールします。

```
yarn add react-error-boundary
```

## コンポーネントの作成
上記のライブラリを使い、ErrorBoundaryコンポーネントを`src/components/layout`配下に作成します。
[公式リポジトリ](https://github.com/bvaughn/react-error-boundary)の例を参考に作成しました。

```tsx:LayoutErrorBoundary.tsx
import type { FC, ReactNode, VFC } from 'react'
import { ErrorBoundary } from 'react-error-boundary'

type ErrorFallbackProps = {
  error: Error
  resetErrorBoundary: () => void
}

const ErrorFallback: VFC<ErrorFallbackProps> = (props) => {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{props.error.message}</pre>
      <button onClick={props.resetErrorBoundary}>Try again</button>
    </div>
  )
}

type LayoutErrorBoundaryProps = {
  children: ReactNode
}

export const LayoutErrorBoundary: FC<LayoutErrorBoundaryProps> = (props) => {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => {
        // reset the state of your app so the error doesn't happen again
      }}
    >
      {props.children}
    </ErrorBoundary>
  )
}
```

今回外部から読み込みはしていないのですが、使い方としてはヘッダーやサイドバーなど、ページ間で共通のレイアウトを除いた
メインのコンテンツとなる部分のコンポーネントをこちらの`LayoutErrorBoundary`でラップすることによりその内部で伝播されたエラーをここでcatchし、`ErrorFallback`コンポーネントを表示させることができます。

# 8. renovateの設定
renovateについては他と手順が少し変わります。
またこちらは現段階で無料ではあるもののクレジットカード登録、又はPaypalによる支払い情報を登録する必要があるため自己責任にてよろしくお願いいたします。

[公式サイト](https://www.whitesourcesoftware.com/free-developer-tools/renovate/)に行き以下の「Github App」ボタンをクリックします。
![](/images/how-to-make-next-template-repository/renovate-top.png)

画面下部にある「Install it for free」をクリックします。
![](/images/how-to-make-next-template-repository/renovate-install.png)

「Complete order and begin installation」をクリックします。
![](/images/how-to-make-next-template-repository/renovate-link.png)

支払い情報を選択した後、GitHub連携方法を尋ねられます。
ラジオボタンが選択可能なのですが今回は「Only select repositries」にして今回の「next-ts-template」のリポジトリを選択しました。
(全てのリポジトリにするとPRがすごいことになりそう... お気をつけください。)
![](/images/how-to-make-next-template-repository/renovate-github.png)

renovateを初めてPRに入れると程なくしてこのようなPRが自動で作成されます。
これはルートディレクトリに、renovateの設定ファイルである「renovate.json」を作成してくれるものです。
後からよしなにカスタマイズできますが一旦脳死でマージしてしまいましょう。
![](/images/how-to-make-next-template-repository/renovate-pr.png)

これでセットアップ完了となります、今後連携したリポジトリに対してはrenovate botがどんどんPRを投げてくれるようになるかと思います。
また、[こちら](https://docs.renovatebot.com/configuration-options/)に設定ファイルに関するドキュメントがまとまっていますので運用しつつ、設定ファイルの内容は改善していけたらと思います。

# 9. nprogressの設定 (2021/12/15 追記)

## packageインストール

以下のコマンドでパッケージをインストールします。
`nprogress`は型も一緒に。

```
yarn add nprogress
```

```
yarn add -D @types/nprogress
```
## _app.tsxの修正

以下のように修正します。

```tsx
import 'nprogress/nprogress.css'

import type { AppProps } from 'next/app'
import nprogress from 'nprogress'
import { useEffect } from 'react'

// お好みで設定。
// 一瞬で遷移できる場合でもspeed: 400のようにするとバーが必ずアニメーションしてくれる?
nprogress.configure({ showSpinner: false, speed: 400, minimum: 0.25 })

const MyApp = ({ Component, pageProps }: AppProps) => {
  if (process.browser) {
    nprogress.start()
  }

  useEffect(() => {
    nprogress.done()
  })

  return <Component {...pageProps} />
}

export default MyApp

```

## 動作確認

pages配下に1つページをはやして確認したところうまく表示されてくれました！
（簡単でした！）

# さいごに
場合によっては`_document.tsx`を作成したり、独自の`404.tsx`を作成したり、`next-seo`のようなライブラリを入れたり
など考えると入れておきたいケースが多いものが様々あるかとは思いますが、今回は一旦この程度に留めておきたいなと思います。
テンプレートリポジトリがあると、技術をすぐに試すモチベーションが上がったり、様々な環境系ライブラリの知識も身につくので今回自作してみて良かったなと思います。
(それでも他の方のものをほぼ参考にしていたのですが...)

他にもおすすめな便利ツールや設定がありましたら教えていただけると嬉しいです。
最後まで見ていただきありがとうございました！


# 参考にさせていただいたもの
- https://github.com/lightsound/nexst
- https://zenn.dev/yoshiko/articles/99f8047555f700
- https://github.com/cats-oss/scaffdog
- https://ja.reactjs.org/docs/error-boundaries.html
- https://zenn.dev/azukiazusa/articles/60933e9cb1a4bc
- https://zenn.dev/berlysia/articles/5dfa58f282aa14
- https://qiita.com/takiga/items/6ec7b9c9613ec8bf7d51
- https://mo-gu-mo-gu.com/create-next-app-typescript/
- https://nalog.work/minh8zakv
- https://www.npmjs.com/package/eslint-plugin-strict-dependencies
- https://zenn.dev/thiragi/articles/555a644b35ebc1
- https://qiita.com/dtakkiy/items/8d6025c052784ab8eef4
- https://fwywd.com/tech/husky-setup
- https://zenn.dev/yusugomori/articles/51e63a4aa9dc27e21124