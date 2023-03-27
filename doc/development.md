# 開発資料

Next.JSでアプリケーションの開発を行う手順を記録します。

## 前提条件

- asdf

## 開発環境を構築

asdf-nodejs pluginをインストールする。

```
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git
```

インストールしたasdf pluginを確認する。

```
asdf plugin lit
```

asdfでnodejs 16.17.1 LTSをインストールする。

`.tool-versions` ファイルを作成して、下記のように記述する。

```
nodejs 16.17.1
```

asdfでnodejsをインストールする。

```
asdf install
```

インストールしたnodejsのバージョンを確認する。

```
node --version
```

## Next.jsのプロジェクトを作成

TypeScript対応のNext.jsアプリケーションのプロジェクトを作成する。

```
npx create-next-app@latest --ts
```

開発サーバーを起動する。

```
npm run dev
```

http://localhost:3000 にアクセスしてページが表示されるか確認する。

`src` ディレクトリを作成して `pages` ディレクトリと `styles` ディレクトリを移動する。

ディレクトリ構造の変更に合わせて、`tsconfig.json`を次の通り編集する。

1. baseURLオプションにsrcを指定し、基準となるディレクトリを変更する。
2. includeオプションに`"src/**/*.ts"`、`"src/**/*.tsx"`を指定し、コンパイル対象とする。
3. TypeScriptの厳格な型チェックを行うためにstrictオプションをオンにする。

## styled-componentsの設定

styled-componentsをインストール

```
npm install styled-components
npm install --save-dev @types/styled-components
```

`next.config.js`を作成し以下のように記述する。

```
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  compiler: {
    // styledComponentsの有効化
    styledComponents: true,
  },
}

module.exports = nextConfig
```

pagesに_document.tsxを作成し、SSRでもstyled-componentsが動作するように設定する。

`pages/_app.tsx`を編集し、`createGlobalStyle`を使って、グローバルにスタイルを適用させる。

## ESLintの設定

リントツールとしてESLintとフォーマッターのPrettier、その他プラグインをインストールする。

```
npm install --save-dev prettier eslint typescript-eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-prettier eslint-plugin-prettier eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-import
```

`.eslintrc.json` を編集してrecommendedなルールを適用する。

次は、`package.json`を編集してnpmスクリプトにlintコマンドとformatコマンドを追加する。

## VS Codeの設定

次は、VS CodeのESLint extensionをインストールする。
`.vscode/settings.json`を編集して、ファイルの保存時に自動的にESLintのエラーを修正する。

TypeScriptのインデントをスペース2に変更する。

## Storybookの設定

Storybookをインストールするため、プロジェクトのルートディレクトリで生成する。

```
npx sb init
```

次にその他のプラグインなどのライブラリを導入する。

```
npm install --save-dev @storybook/addon-postcss tsconfig-paths-webpack-plugin @babel/plugin-proposal-class-properties @babel/plugin-proposal-private-methods @babel/plugin-proposal-private-property-in-object @mdx-js/react
```

Storybookを以下のコマンドで起動し、正しく画面が表示されるか確認する。

```
npm run storybook
```

### アセット配置の準備

Storybookに対してアセットを配置するため `.storybook/public` を作成する。

```
mkdir .storybook/public
```

次に`.storybook/main.js`を編集し、`staticDirs`オプションを追加する。これにより、静的ファイルを配置するディレクトリを指定する。

### StorybookのThemeの設定

全体のThemeを設定する。
`src/themes`ディレクトリ以下にThemeファイルを作成する。

### Storybookの設定ファイルの編集

次にStorybookの設定ファイルを編集し、Themeの適用、簡易のリセットCSS、Next.jsのnext/imageの差し替えを行う。next/imageは画像を最適化してくれる画像コンポートネントだが、Storybook上では動作しないため、強制的に通常の画像と差し替える。

`.storybook/preview.js`を編集する。

アドオンは`.storybook/main.js`に追加する。wepackFinalの設定は必要なアドオンを導入し、tsconfigの設定を引き継ぐためのものです。

## React Hook Fromの導入

React Hook Formはフォームバリデーションライブラリです。パフォーマンス・柔軟性・拡張性の面で優れています。

```
npm install react-hook-form
```

## SWRの導入

SWRはVercelが開発している、データ取得のためのReact Hooksライブラリです。
CSRを効率的に実現するために導入します。

```
npm install swr
```

## React Content Loaderの導入

React Content Loaderはローディングのためのプレースホルダーを簡単に作成できるライブラリです。

```
npm install react-content-loader
npm install --save-dev @types/react-content-loader
```

## Material Iconsの導入

Meterial IconsはUIライブラリMaterial UIのコンポーネントの一つです。今回はアイコンの表示の用途でのみ使用します。
Material UIが依存しているemotion関連のライブラリも合わせてインストールします。

```
npm install @mui/material @mui/icons-material @emotion/react @emotion/styled
```

## 環境変数

環境変数の設定として`.env`ファイルを用意して、プロジェクトのルートに配置します。

`API_BASE_URL`はこの後に説明するJSON ServerのベースURL、`NEXT_PUBLIC_API_BASE_PATH`はこの後で説明するNext.jsのURL Rewrite機能で使用する変数です。

## テスト環境構築

React/Next.jsのテスト環境を構築します。

```
npm install --save-dev @testing-library/jest-dom @testing-library/react jest jest-environment-jsdom
```

`jest.setup.js`と`jest.config.js`をプロジェクトのルートに追加します。

`package.json`に`test`コマンドを追加して、テストを実行します。

```
npm run test
```

最後に`next.config.js`に、ビルド時にReact Testing Libraryで使用する`data-testid`属性を削除する設定を追加します。
この属性は本番環境では必要がないので、`reactRemoveProperties`というオプションを利用して削除します。

## JSON Serverの設定

JSON ServerとはREST APIのダミーエンドポイントを作成するためのツールです。

別ディレクトリにサンプルをクローンして使用します。

```
git clone https://github.com/gihyo-book/ts-nextbook-json.git
```

クローンしたディレクトリに移動してサーバーを起動します。

```
npm ci
npm start
```

以下のAPIが使用できます。

| API | パス | HTTPメソッド | 説明 |
| --- | --- | --- | --- |
| 認証API | /auth/signin | POST | サインイン |
| 認証API | /auth/signout | POST | サインアウト |
| ユーザーAPI | /users | GET | 一覧取得 |
| ユーザーAPI | /users/{id} | GET | 個別取得 |
| ユーザーAPI | /users/me | GET | 認証済みのユーザーを取得 |
| プロダクトAPI | /products | GET,POST | 一覧取得、新規追加 |
| プロダクトAPI | /products/{id} | GET | 個別取得 |
| 購入API | /purchases | POST | 商品購入 |

cURLを使用してusersのデータを取得するAPIをコールします。

```
curl -X GET -i https://localhost:8000/users
```
