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

## TypeScript
`tsconfig.json` を作成して `npm run dev` すると勝手にTypeScript化してくれるはず……なのだが、何度指示に従っても `npm install --save-dev @types/react` をしろと繰り返される。

```
It looks like you're trying to use TypeScript but do not have the required package(s) installed.

Please install @types/react by running:

        npm install --save-dev @types/react

If you are not trying to use TypeScript, please remove the tsconfig.json file from your package root (and any TypeScript files in your pages directory).
```

おそらく同じバグ：

[javascript \- Next\.js TypeScript Error: You do not have the required packages installed \- Stack Overflow](https://stackoverflow.com/questions/71842787/next-js-typescript-error-you-do-not-have-the-required-packages-installed)

面倒なので一度消して

```
npx create-next-app@latest --ts
```

でやりなおす。

[Basic Features: TypeScript \| Next\.js](https://nextjs.org/docs/basic-features/typescript#create-next-app-support)

# Permission denied

WSL2（ホスト、Ubuntu）上: ログインユーザー

コンテナ上: root

の間の権限の不整合で、ホスト上からボリュームマウントに作成されるファイルを編集できなくなる。

以下の方法で解決。

ホスト上の `/etc/docker/daemon.json` に
```json
{
        "userns-remap": "default"
}
```
を記述（nsはたぶん name space ）。

`/etc/subuid` と `/etc/subgid` にともに
```
dockremap:1000:65536
```
を追記。

docker-compose.ymlと同階層に.envファイルを作成して
```
USER_ID=1000
GROUP_ID=1000
```
を記述。

docker-compose.ymlに
```yaml
    volumes:
      - /etc/passwd:/etc/passwd:ro
      - /etc/group:/etc/group:ro
    user: ${USER_ID}:${GROUP_ID}
```
を追記。

Dockerを再起動。

`docker-compose up -d` でコンテナを立ち上げる。


## 参考
- [ユーザ名前空間でコンテナを分離 — Docker\-docs\-ja 19\.03 ドキュメント](https://docs.docker.jp/v19.03/engine/security/userns-remap.html)
- [Tavi's Travelog \- Dockerでのホストとコンテナの権限問題について](http://blog.tavi-travelog.net/2020/08/10/issue-permission-on-docker)
- [宇宙の晴れ上がり: Ubuntu 18\.04のDockerでUser remapを使う](http://transparent-to-radiation.blogspot.com/2018/06/ubuntu-1804dockeruser-remap.html)
- [Declare default environment variables in file \| Docker Documentation](https://docs.docker.com/compose/env-file/)
- [dockerでvolumeをマウントしたときのファイルのowner問題 \- Qiita](https://qiita.com/yohm/items/047b2e68d008ebb0f001)
- [bash \- How to set uid and gid in Docker Compose? \- Stack Overflow](https://stackoverflow.com/questions/56844746/how-to-set-uid-and-gid-in-docker-compose)


# Permisson denied

↑解決していなかった。npm installで/home下にディレクトリを作ろうとするが、root権限が無くてこける。

docker-compose.ymlに

```yml
    privileged: true
```

を追記する。うまくいく。

サンドボックス化を破るので良くないという説を見たが今は考えない。

