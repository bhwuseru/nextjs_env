# Next.jsの環境構築
- [Next.jsのdocker-compose環境構築手順](#nextjsのdocker-compose環境構築手順)
- [本番環境のビルド時の注意点](#本番環境のビルド時の注意点)
- [make自動化スクリプト実行手順](#make自動化スクリプト実行手順)

## Next.jsのdocker-compose環境構築手順
1. ルートディレクトリ直下の`.envrc.example`を.envrcにリネームしてポート設定やプロジェクト名などの設定を編集をする。<br>
- **補足**<br>
.envrc定義情報を元にdocker-compose.ymlが参照する.envファイルを作成する。<br>
**ポートはホスト側のポートと衝突しないようにする。**<br>

## 本番環境のビルド方法
1. 静的ファイルのビルド方法と設定<br>
.devcontainer/node/html/next.config.js.exampleを参考にビルド設定をする

	```
	/** @type {import('next').NextConfig} */

	const isDevelopmet = process.env.NODE_ENV !== 'production'    
	const nextConfig = {
		// output/index.htmlを生成する場合は下記を追記
		output: 'export',
		images: {
			unoptimized: true
		},
		assetPrefix: isDevelopmet ? '' : '',   
	}

	module.exports = nextConfig
	```

2. 設定後に`npm run build`または`yarn build`を実行すると静的ファイルが生成される。
	```
	# 実行後にout/index.htmlが生成される
	yarn build
	```

1. メモリ不足でビルドできない。
環境変数を設定することでnodejsのメモリ使用量を設定できる。
	```
	export NODE_OPTIONS="--max-old-space-size=1024"
	```
## make自動化スクリプト実行手順
以下コマンドを実行するとdockerのコンテナを自動で作成と削除を実行してくれる。
1. makeが導入されていない場合は以下コマンドで導入する。
    ```
    sudo apt install make
    ```
2. .envrcファイルの定義情報を元にdocker-composeの開発環境を構築する。
	```
    make container-init
	```
3. .envrcで定義した環境変数`${PROJECT_NAME}-node`というdockerコンテナが作成されているので、ダッシュボードからアタッチする。<br> 
または下記コマンドを実行するとコンテナ内に入れる。
	```
	docker exec -it ${PROJECT_NAME}-node  /bin/bash  
	```
4. コンテナ内に入るとinstall.shスクリプトが配置されているのでプロジェクトがまだ一度も作成されていない場合は以下を実行する。
	```
	. ./install.sh
	```
5. .envrc定義情報の以下ポートにアクセスできる。<br>
 **補足**<br>
 proxy service 公開側ポートは.devcontainer/node直下に存在するnext.config.js.example内容をnext.config.jsに上書きしnpm run buildを実行すること。
	```
	# PhpMyadmin servic 公開側ポート
	export PHP_MYADMIN_PUBLIC_PORT=
	# proxy service 公開側ポート npm run build
	export PROXY_PUBLIC_PORT=
	# 開発サーバーのポート npm run dev
	export NODE_PORT=
	```

6. docker-composeの環境を一旦削除して初期状態に戻したい場合は以下を実行する。
    ```
    make container-remove 
    ```

