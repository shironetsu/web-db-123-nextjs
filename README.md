特集2: 「Reactベースの柔軟・省設定フレームワーク　いまどきNext.js」をやる

[WEB\+DB PRESS Vol\.123｜技術評論社](https://gihyo.jp/magazine/wdpress/archive/2021/vol123)




# メモ

## Vercel
Vercel上の [New Project – Vercel](https://vercel.com/new) からNext.jsのテンプレートを作ろうとすると、
指定したリポジトリへのクローンのステップでこける。

仕方がないので、`vercel` CLIをダウンロードしてファイル一揃いをコピー。

1. `npm i -g vercel` [Download Vercel CLI – Vercel](https://vercel.com/cli)
2. `vercel init nextjs src`

## Bus error
WSL2 Ubuntu 22.04上で作業。

`npm run dev` が `Bus error`とだけメッセージを吐いて異常停止する。

→`package-lock.json`, `node_modules`, `.next` を削除して再試行すると成功。

['Bus Error' while running 'npm run build' or 'npm run dev' in Ubuntu 20\.04 server : nextjs](https://www.reddit.com/r/nextjs/comments/ry8sl1/bus_error_while_running_npm_run_build_or_npm_run/)