Next.js と Flask を使った Web アプリケーションのセットアップ
このプロジェクトでは、Next.js をフロントエンド、Flask をバックエンドとして使用した簡単な Web アプリケーションを構築します。初心者向けに詳細な手順を記載しています。

【環境構築】
1. 必要なライブラリとバージョン

〇Next.js

Node.js: v14.x または v16.x

Next.js: v12.x

〇Flask
Python: 3.8 以上

Flask: v2.1.x

Flask-CORS: v3.x

2. Next.js のセットアップ
2.1 プロジェクトの作成
Node.js がインストールされていることを確認してください。インストールされていない場合は、Node.js の公式サイトからインストールしてください。
ターミナルを開き、プロジェクトを作成したいディレクトリに移動して以下のコマンドを実行します。

bash
npx create-next-app@12 nextjs-flask-app

ディレクトリに移動します。

bash
cd nextjs-flask-app


2.2 必要なパッケージのインストール
プロジェクトのディレクトリで以下のコマンドを実行して、必要なパッケージをインストールします。

bash
npm install


2.3 Next.js の立ち上げ
プロジェクトディレクトリ内で以下のコマンドを実行して、Next.js 開発サーバーを起動します。

bash
npm run dev

ブラウザで http://localhost:3000 にアクセスすると、Next.js が立ち上がります。


3. Flask のセットアップ
3.1 仮想環境の作成と有効化
Python の仮想環境を作成します。

bash
python -m venv venv
仮想環境を有効化します。

macOS/Linux:
bash
source venv/bin/activate


Windows:
bash
venv\Scripts\activate


3.2 必要なパッケージのインストール
以下のコマンドを実行して Flask と Flask-CORS をインストールします。

bash
pip install Flask==2.1.3 flask-cors==3.0.10


3.3 Flask サーバーの起動
app.py という名前のファイルを作成し、以下の内容を記述します。

python

#モジュールインポート：アプリに必要なモジュールを使用できるようにする
from flask import Flask, request, jsonify
from flask_cors import CORS

#アプリの作成：appでFlaskフレームワークを利用できるようにする
app = Flask(__name__)
CORS(app)

#エンドポイントの設定：フロントエンドとの連携部分
@app.route('/api/hello', methods=['GET'])
def hello():
    return jsonify({'message': 'Hello World'})

@app.route('/api/echo', methods=['POST'])
def echo():
    data = request.json
    return jsonify({'message': data['input']})

#アプリの起動：app.runでアプリを実行する。
if __name__ == '__main__':
    app.run(debug=True)

以下のコマンドを実行して Flask サーバーを起動します。

bash
python app.py

ブラウザで http://localhost:5000/api/hello にアクセスして、{"message": "Hello World"} が表示されることを確認します。


【Next.js で行うこと】

1. GET メソッドの実装
ブラウザで http://localhost:3000/api/hello にアクセスすると、自動で "Hello World" が表示されます。

2. POST メソッドの実装
フロントエンドの入力欄にテキストを入力し、"送信" ボタンを押すと、そのテキストがバックエンドに送信され、同じ内容が画面に表示されます。

【Flask で行うこと】

1. GET メソッドで「Hello World」を返す
/api/hello エンドポイントに GET リクエストを送ると、{"message": "Hello World"} が返されます。

2. POST メソッドで入力された内容を返す
/api/echo エンドポイントに POST リクエストを送ると、送信されたデータがそのまま返されます。


＊以下繋ぎこみ関してより詳しく説明します。上までの説明で完了していたら以下は不要です。
Next.jsとFlaskのセットアップが完了した後に、両方のサーバーを繋ぎ込む手順を詳しく説明します。


【繋ぎ込み手順: Next.jsとFlaskを連携する】

1. Next.jsフロントエンドの構築
まず、Next.jsでフロントエンドのページを作成し、ユーザーが入力した内容をFlaskのAPIに送信し、その応答を表示する部分を実装します。

新しいページの作成

frontend/pages フォルダ内に、index.js というファイルが既に存在している場合は、それを開きます。存在しない場合は新規作成します。

以下のコードを index.js に記述します。

javascript


#インポート：・アプリに必要なコンポーネント,関数等を使用できるようにする
import { useState } from 'react';


#ページコンポーネント（ページ生成）：export default:このモジュールからエクスポートされるデフォルト値を指定
export default function Home() {
  const [input, setInput] = useState('');
  const [response, setResponse] = useState('');
  

#変数、定数、関数を定義

  const handleSubmit = async (e) => {
    e.preventDefault();
    const res = await fetch('http://localhost:5000/api/echo', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ input }),
    });
    const data = await res.json();
    setResponse(data.message);
  };

#レンダリングする内容を定義

  return (
    <div>
      <h1>Next.jsとFlaskの連携アプリ</h1>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="テキストを入力してください"
        />
        <button type="submit">送信</button>
      </form>
      {response && <p>サーバーからの応答: {response}</p>}
    </div>
  );
}


解説:
useStateを使って、ユーザーの入力(input)と、Flaskサーバーからの応答(response)を管理しています。
handleSubmit関数は、フォームが送信されたときにFlaskのAPIにPOSTリクエストを送信し、返ってきたデータを表示します。


動作確認

Next.jsサーバーが動作している状態で、ブラウザで http://localhost:3000 にアクセスします。
テキストボックスに文字を入力して「送信」ボタンを押すと、FlaskのAPIからの応答が表示されることを確認します。


2. Flaskバックエンドの構築
次に、Flaskで受け取ったデータに応じた応答を返すAPIを確認します。Flask側のコードはすでにセットアップされているので、ここでは確認作業を行います。


app.py の確認

backend/app.py ファイルを開き、以下のコードが正しく記述されているか確認します。


python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/hello', methods=['GET'])
def hello_world():
    return jsonify(message='Hello World')

@app.route('/api/echo', methods=['POST'])
def echo():
    data = request.get_json()
    return jsonify(message=data['input'])

if __name__ == '__main__':
    app.run(debug=True)
    

Flaskサーバーの動作確認
Flaskサーバーが動作している状態で、Postmanやブラウザで http://localhost:5000/api/hello にアクセスし、Hello World が返ってくるか確認します。
また、POSTメソッドで http://localhost:5000/api/echo にテキストを送信し、そのままのテキストが返ってくることを確認します。

3. Next.jsとFlaskの連携動作確認
両方のサーバーを起動

すでにNext.jsサーバー（http://localhost:3000）　と　Flaskサーバー（http://localhost:5000）が起動していることを確認します。
フロントエンドからバックエンドへのデータ送信

ブラウザで http://localhost:3000 にアクセスし、フォームにテキストを入力して送信します。
テキストを送信すると、Flaskサーバーからの応答が表示されることを確認します。

まとめ
Next.jsはフロントエンド部分を担当し、ユーザーからの入力を収集します。
Flaskはバックエンド部分を担当し、APIを通じてデータのやり取りを行います。
この連携により、フロントエンドとバックエンドがシームレスに動作し、ユーザーインターフェースが機能するようになります。
この手順に従うことで、Next.jsとFlaskを連携させ、エンドツーエンドで動作するWebアプリケーションを構築できます。
