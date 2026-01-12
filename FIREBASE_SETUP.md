# Firebase クラウド同期機能 セットアップガイド

このアプリにはFirebaseを使用したクラウド同期機能が実装されています。

## 📋 セットアップ手順

### 1. Firebaseプロジェクトの作成

1. [Firebase Console](https://console.firebase.google.com/) にアクセス
2. 「プロジェクトを追加」をクリック
3. プロジェクト名を入力（例: puppeteer-app）
4. Google Analyticsは任意で設定
5. プロジェクトを作成

### 2. Webアプリの追加

1. Firebaseプロジェクトのダッシュボードで「</> (Web)」アイコンをクリック
2. アプリのニックネームを入力（例: Puppeteer Web App）
3. 「Firebase Hosting」のチェックは不要
4. 「アプリを登録」をクリック
5. 表示される `firebaseConfig` オブジェクトをコピー

### 3. Firestoreの有効化

1. 左メニューから「Firestore Database」を選択
2. 「データベースの作成」をクリック
3. セキュリティルールで「テストモードで開始」を選択（後で本番モードに変更可能）
4. ロケーションを選択（asia-northeast1 推奨）
5. 「有効にする」をクリック

### 4. Authenticationの設定

1. 左メニューから「Authentication」を選択
2. 「始める」をクリック
3. 「Sign-in method」タブを選択
4. 「Google」を選択して有効化
5. プロジェクトのサポートメールを選択
6. 「保存」をクリック

### 5. Firestoreセキュリティルールの設定

1. Firestore Databaseの「ルール」タブを選択
2. 以下のルールを設定:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // ユーザーは自分のデータのみ読み書き可能
    match /users/{userId}/apps/{appId}/chunks/{chunkId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

3. 「公開」をクリック

### 6. アプリの設定

`index.html` ファイルを開き、以下の部分を見つけて、手順2でコピーした値に置き換えてください：

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",              // ← あなたのAPIキー
    authDomain: "YOUR_AUTH_DOMAIN",      // ← あなたのAuth Domain
    projectId: "YOUR_PROJECT_ID",        // ← あなたのProject ID
    storageBucket: "YOUR_STORAGE_BUCKET", // ← あなたのStorage Bucket
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID", // ← あなたのMessaging Sender ID
    appId: "YOUR_APP_ID"                 // ← あなたのApp ID
};
```

## 🚀 使い方

### ログイン
1. 画面右下の「☁️ クラウド同期」パネルを確認
2. 「🔐 Googleでログイン」ボタンをクリック
3. Googleアカウントでログイン

### データの保存
1. ログイン後、「⬆️ クラウドへ保存」ボタンをクリック
2. 全てのキャラクター、世界観、設定がクラウドに保存されます
3. データは800KBごとにチャンク分割されて保存されます

### データの復元
1. ログイン後、「⬇️ クラウドから復元」ボタンをクリック
2. クラウドのデータが取得され、ローカルに復元されます
3. 自動的にページがリロードされ、復元されたデータが表示されます

## 🔧 技術仕様

- **データ保存先**: `users/{uid}/apps/puppeteer_v8/chunks/{index}`
- **チャンクサイズ**: 800KB (819,200 bytes)
- **アップロード方式**: Promise.allによる並列アップロード
- **ダウンロード方式**: インデックス順序でチャンクを結合

## ⚠️ 注意事項

- データサイズが大きい場合、アップロード/ダウンロードに時間がかかることがあります
- ネットワーク接続が必要です
- 同じGoogleアカウントでログインすれば、別のデバイスからもデータにアクセスできます
- 定期的にクラウドへバックアップすることを推奨します

## 🔒 セキュリティ

- Firestoreセキュリティルールにより、ユーザーは自分のデータのみアクセス可能
- 認証されていないユーザーはデータにアクセスできません
- 本番環境では必ずセキュリティルールを適切に設定してください

## 🐛 トラブルシューティング

### ログインできない
- Firebase Consoleで認証が有効になっているか確認
- ブラウザのポップアップブロックを無効にする
- ブラウザのCookieが有効になっているか確認

### アップロード/ダウンロードが失敗する
- Firestoreが有効になっているか確認
- セキュリティルールが正しく設定されているか確認
- ブラウザのコンソールでエラーメッセージを確認

### データが復元されない
- クラウドにデータが保存されているか確認（Firebase Console で確認可能）
- ブラウザのlocalStorageが有効になっているか確認
