# 少女サクラと不思議のダンジョン v0.122.0

この版は、ゲーム本体HTMLとBGMファイルを分離したGitHub／GitHub Pages向け構成です。

## フォルダ構成

```text
sakura-dungeon/
├── index.html
├── README.md
├── audio_assets_manifest.json
└── assets/
    └── audio/
        ├── sakura-theme.ogg
        ├── sakura-theme.mp3
        ├── novis-forest.ogg
        ├── novis-forest.mp3
        ├── mysterious-dungeon.ogg
        ├── mysterious-dungeon.mp3
        ├── underwater-sewer.ogg
        ├── underwater-sewer.mp3
        ├── misty-valley.ogg
        └── misty-valley.mp3
```

**重要：** `assets/audio/` のフォルダ名と相対パスを維持してください。`index.html` だけではBGMは再生できません。ゲーム本体とこの音源フォルダを同じリポジトリに登録してください。

## GitHub Pagesで公開する方法

1. ZIPを展開します。
2. 展開したフォルダ内の `index.html` と `assets/` フォルダを、GitHubリポジトリのルートにアップロードします。
3. `README.md` と `audio_assets_manifest.json` も一緒に登録しておくと、構成と音源の管理が容易になります。
4. GitHubリポジトリの **Settings → Pages** で、公開元ブランチと `/ (root)` を選んで保存します。
5. 公開されたページを開き、タイトル画面と4つのダンジョンでBGMを確認します。

リポジトリのサブパスで公開されても動くように、音源URLは `assets/audio/...` という相対パスにしています。

## PCでローカル確認する方法

HTMLを `file://` で直接開く方法では、ブラウザのセキュリティ制限により外部音源の `fetch` が拒否される場合があります。ローカルHTTPサーバーを使ってください。

WindowsのPowerShellで、このフォルダに移動して次を実行します。

```powershell
py -m http.server 8000
```

その後、ブラウザで `http://localhost:8000/` を開きます。

## BGMの読み込み方式

- OGG：対応環境でWeb Audio APIから取得・デコードし、既存のループ用クロスフェード処理を適用します。
- MP3：OGGのデコードに失敗した場合、またはWeb Audioが利用できない環境でフォールバックします。
- タイトル曲と、入ったダンジョンのBGMを必要になった時点で読み込みます。全ダンジョンの音源を起動時に一括取得しません。
- 既存の音量設定を維持します。タイトル曲・従来ダンジョンは0.25、濃霧の渓谷は0.42です。
- ブラウザの自動再生制限に対するユーザー操作後の再試行処理を維持しています。

## 新しいダンジョンBGMを追加するとき

1. `assets/audio/` に、同じ曲の `.ogg` と `.mp3` を配置します。
2. `index.html` の `DUNGEON_THEME_OGG_URLS` と `DUNGEON_THEME_MP3_URLS` に、同じダンジョンIDで相対パスを追加します。
3. `DUNGEON_CONFIGS` の `musicId` がそのIDを指すことを確認します。
4. BGMの取得・デコード・MP3フォールバック・ループを確認します。

**音源をBase64に変換してHTMLへ埋め込まないでください。** 以後も外部ファイルとして管理することで、HTML本体の容量がBGM追加に伴って膨らむことを防げます。

## セーブデータ

`SAVE_FORMAT_VERSION = 1` を維持しています。今回の変更はBGMの配布・読み込み方式のみで、セーブデータ形式は変更していません。
