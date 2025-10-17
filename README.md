# VoiceInputWidget

マウスホバーで音声認識を開始し、認識結果をテキストエリアに入力する再利用可能なJavaScriptウィジェット

## 特徴

- 🎤 **マウスホバーで音声入力**: トリガーブロックにホバーするだけで音声認識開始
- 💬 **吹き出し表示**: 認識中のテキストをリアルタイムで表示
- 🇯🇵 **日本語形態素解析**: Kuromoji.jsによる名詞抽出（オプション）
- 🔄 **循環入力**: 複数のテキストエリアに順番に入力
- 🌐 **ブラウザ互換性**: プライバシー重視ブラウザにも対応（エラー表示）

## 必要な依存関係

- **Web Speech API** (Chrome、Edge、Safari対応)
- **Kuromoji.js** (名詞抽出機能を使う場合のみ)

## インストール

### 1. ファイルをプロジェクトに追加

```
your-project/
├── voice-input-widget/
│   ├── voice-input-widget.js
│   └── voice-input-widget.css
└── node_modules/
    └── kuromoji/  # 名詞抽出を使う場合
```

### 2. HTMLに読み込み

```html
<!-- Kuromoji.js (名詞抽出を使う場合) -->
<script src="node_modules/kuromoji/build/kuromoji.js"></script>

<!-- VoiceInputWidget -->
<link rel="stylesheet" href="voice-input-widget/voice-input-widget.css">
<script src="voice-input-widget/voice-input-widget.js"></script>
```

## 基本的な使い方

### HTMLでテキストエリアを準備

```html
<input type="text" id="word1">
<input type="text" id="word2">
<input type="text" id="word3">
```

### JavaScriptで初期化

```javascript
const widget = new VoiceInputWidget({
  targetIds: ['word1', 'word2', 'word3']
});
```

これだけで動作します！

## オプション設定

```javascript
const widget = new VoiceInputWidget({
  // 必須: 入力先のテキストエリアのID配列
  targetIds: ['word1', 'word2', 'word3'],

  // オプション: 文字数制限（デフォルト: 20）
  maxLength: 20,

  // オプション: 名詞抽出の有効化（デフォルト: true）
  extractNoun: true,

  // オプション: トリガーブロックのテキスト
  triggerText: 'ホバーで音声入力',
  activeText: '音声入力中...',
  unsupportedText: '音声認識が利用できません',

  // オプション: Kuromojiの辞書パス
  kuromojiDicPath: 'node_modules/kuromoji/dict/',

  // オプション: カスタムコールバック
  onWordExtracted: (word) => {
    console.log('抽出された単語:', word);
  }
});
```

## 動作仕様

### 通常モード（20文字以下）
認識した文字列全体をそのままテキストエリアに入力します。

**例**: 「こんにちは」→ `こんにちは`

### 名詞抽出モード（20文字超）
`extractNoun: true` かつ文字列が `maxLength` を超える場合、最初の連続名詞句を抽出して入力します。

**例**: 「今日は1000円のチャーシューを買いました」→ `1000円チャーシュー`

### 循環入力
指定されたテキストエリアに順番に入力し、最後まで到達したら最初に戻ります。

## ブラウザ互換性

### 対応ブラウザ
- ✅ Chrome
- ✅ Edge
- ✅ Safari (通常モード)

### 非対応ブラウザ
- ❌ Brave (デフォルト設定)
- ❌ Firefox (部分対応)
- ❌ Tor Browser
- ❌ LibreWolf

非対応ブラウザでは、トリガーブロックに案内メッセージが表示されます。

## メソッド

### `destroy()`
ウィジェットを破棄し、DOM要素を削除します。

```javascript
widget.destroy();
```

## スタイルのカスタマイズ

`voice-input-widget.css` を編集してスタイルを変更できます。

主なクラス:
- `.voice-input-trigger-block` - トリガーブロック
- `.voice-input-recognition-area` - 吹き出し
- `.voice-input-trigger-text` - トリガーテキスト

## トラブルシューティング

### 音声認識が動作しない
1. ChromeまたはEdgeを使用していますか？
2. マイク権限を許可していますか？
3. `localhost` または HTTPS で実行していますか？

### Kuromojiが動作しない
1. `kuromoji.js` を正しく読み込んでいますか？
2. 辞書パス（`kuromojiDicPath`）は正しいですか？
3. `npm install kuromoji` を実行しましたか？

## ライセンス

ISC

## 作者

Generated with Claude Code
