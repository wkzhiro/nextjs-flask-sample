Next.js と Flask を使った Web アプリケーションのセットアップ
このプロジェクトでは、Next.js をフロントエンド、Flask をバックエンドとして使用した簡単な Web アプリケーションを構築します。初心者向けに詳細な手順を記載しています。

【環境構築】
1. 必要なライブラリとバージョン

〇Next.js

・Node.js: v14.x または v16.x
Node.js がインストールされていることを確認してください。インストールされていない場合は、Node.js の公式サイトからインストールしてください。
参考サイト：https://qiita.com/sefoo0104/items/0653c935ea4a4db9dc2b

・Next.js: v12.x

〇Flask
・Python: 3.8 以上
・Flask: v2.3.x
・Flask-CORS: v3.x

〇GitHubのクローン
```
git clone https://github.com/Daiki-ux/8th_step3-2.git
cd 8th_step3-2
```

※Zipでダウンロードしても可

2. Next.js のセットアップ

2.1 プロジェクトの作成
プロジェクトを作成する場合は、以下のコマンドを入力してください。
ターミナルを開き、プロジェクトを作成したいディレクトリに移動して以下のコマンドを実行します。
```bash
npx create-next-app@12 nextjs-flask-app
```
※必要なものはインストールを実施（yを入力して、Enter）

2.2 nextjsのプロジェクトのディレクトリに移動します。
```bash
cd nextjs-flask-app
```
2.3 必要なパッケージのインストール
プロジェクトのディレクトリで以下のコマンドを実行して、必要なパッケージをインストールします。
```bash
npm install
```

2.4 Next.js の立ち上げ
プロジェクトディレクトリ内で以下のコマンドを実行して、Next.js 開発サーバーを起動します。
```bash
npm run dev
```

2.5 動作確認
ブラウザで http://localhost:3000 にアクセスすると、Next.js が起動していることを確認できます。
Next.jsとFlaskの連携アプリ というページが表示されれば成功です!
（プロジェクトを起動した場合はWelcome to Next.jsというページが表示されます。）

（2.6 ページの作成）
プロジェクトから作成された方は、pases/index.jsをGitHubのコードをコピーしてください。

3. Flask のセットアップ
3.0 backendのフォルダに移行します。   
```bash
cd backend
```

3.1 仮想環境の作成と有効化
Python の仮想環境を作成します。

```bash
python -m venv venv
```

3.2仮想環境を有効化します。

macOS/Linuxの場合:
```bash
source venv/bin/activate
```
Windowsの場合:
```bash
venv\Scripts\activate
```

3.3 必要なパッケージのインストール

以下のコマンドを実行して Flask と Flask-CORS をインストールします。

```bash
pip install Flask==2.3.3 flask-cors==3.0.10
```

3.4 app.pyの作成app.py という名前のファイルを作成し、以下の内容を記述します。

```python

#モジュールインポート：アプリに必要なモジュールを使用できるようにする
from flask import Flask, request, jsonify
from flask_cors import CORS

#アプリの作成：appでFlaskフレームワークを利用できるようにする
app = Flask(__name__)
CORS(app)

#エンドポイントの設定：フロントエンドとの連携部分
@app.route('/', methods=['GET'])
def hello():
    return jsonify({'message': 'Flask start! '})

@app.route('/api/hello', methods=['GET'])
def hello():
    return jsonify({'message': 'Hello World by Flask'})

@app.route('/api/echo', methods=['POST'])
def echo():
    data = request.json
    return jsonify({'message': data['input']})

#アプリの起動：app.runでアプリを実行する。 
if __name__ == '__main__':
    app.run(debug=True)
```

3.5以下のコマンドを実行して Flask サーバーを起動します。

```bash
python app.py
```

ブラウザで http://localhost:5000/ にアクセスして、{"message": "Flask start!"} が表示されることを確認します。

3.6 API（GET）の確認
ブラウザで http://localhost:5000/api/hello にアクセスして、{"message": "Hello World"} が表示されることを確認します。

4．Next.jsの実装
Next.jsからFlaskにリクエスト（GETやPOST）を送り、Flaskからのレスポンスを受け取る実装を行います。

4.1 GET メソッドの実装
〇Next.js(http://localhost:3000)の動き
http://localhost:5000/api/helloにGETリクエストを送り、Flaskからのレスポンス（{"message": "Hello World"}）が返されます。
これをNext.jsで受け取り、画面に表示します。

〇Flask(http://localhost:5000/)の動き
http://localhost:5000/api/helloに対するGETリクエストを受け取り、（{"message": "Hello World"}を返します。

該当するコードは以下の部分です。
---bash
@app.route('/api/hello', methods=['GET'])
def hello():
    return jsonify({'message': 'Hello World'})
---

4．2. POST メソッドの実装
フロントエンドの入力欄にテキストを入力し、"送信" ボタンを押すと、そのテキストがバックエンドに送信され、同じ内容が画面に表示されます。

〇Next.js(http://localhost:3000)の動き
http://localhost:5000/api/echoにPOSTリクエストを送り、Flaskからのレスポンス（{"message": "入力したテキスト"}）が返されます。
これをNext.jsで受け取り、画面に表示します。

〇Flask(http://localhost:5000/)の動き
http://localhost:5000/api/echoに対するPOSTリクエストを受け取り、（{"message": "入力したテキスト"}を返します。

該当するコードは以下の部分です。
---bash
@app.route('/api/echo', methods=['POST'])
def echo():
    data = request.get_json()
    return jsonify(message=data['input'])
---

【Next.js解説】

```javascript
import { useState } from 'react';

export default function Home() {
  //GETリクエストを送信
  const handleGetRequest = async () => {
    const res = await fetch('http://localhost:5000/api/hello', {
      method: 'GET',
    });
    const data = await res.json();


    // GETリクエストの結果をコンソールに表示
    console.log("GETリクエストの結果:", data.message);

    setGetResponse(data.message);
  };

  //POSTリクエストを送信
  const [input, setInput] = useState('');
  const [getResponse, setGetResponse] = useState('');
  const [postResponse, setPostResponse] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();

    //入力されたデータをコンソールに表示
    console.log("入力情報:", input);

    const res = await fetch('http://localhost:5000/api/echo', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ "message":input }),

    });
    console.log(JSON.stringify({ "message":input }));
    const data = await res.json();

    //バックエンドからのレスポンスをコンソールに表示
    console.log("Backendからのお返事:", data.message);

    setPostResponse(data.message);
  };

  return (
    <div>

      <h1>Next.jsとFlaskの連携アプリ</h1>

      <h2>GETリクエストを送信</h2>
      <button onClick={handleGetRequest}>GETリクエストを送信</button>
      {getResponse && <p>サーバーからのGET応答: {getResponse}</p>}

      <h2>POSTリクエストを送信</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={input}

          onChange={(e) => setInput(e.target.value)}
          placeholder="テキストを入力してください"
        />

        <button type="submit">送信</button>
      </form>

      {postResponse && <p>サーバーからのPOST応答: {postResponse}</p>}
    </div>
  );
}
```

解説:
１．コードの構成
1.1 returnよりも上の部分は関数（requestの方法や、データの受け渡し、定数の定義）を記載しています。
1.2 return以下の部分は、htmlの記載方法に従って、画面の表示を記載しています。ただし、これはjsxというjavascriptでhtmlのように記載できる方法を使用しているためです。

useState: ユーザーの入力(input)と、Flaskサーバーからの応答(response)を管理しています。ReactのuseStateフックを使用して、コンポーネントの状態を管理します。

handleGetRequest関数: GETリクエストをFlaskのAPIに送信し、返ってきたデータをコンソールに表示し、状態を更新します。

handleSubmit関数: フォームが送信されたとき（ボタンを押したとき）にFlaskのAPIにPOSTリクエストを送信し、返ってきたデータを表示します。


まとめ
Next.jsはフロントエンド部分を担当し、ユーザーからの入力を収集します。
Flaskはバックエンド部分を担当し、APIを通じてデータのやり取りを行います。
この連携により、フロントエンドとバックエンドがシームレスに動作し、ユーザーインターフェースが機能するようになります。
この手順に従うことで、Next.jsとFlaskを連携させ、エンドツーエンドで動作するWebアプリケーションを構築できます。
