# 認証API

このドキュメントでは、ユーザー認証と認可に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 |
|--------|--------------|------|------|
| POST | `/auth/login` | ユーザーログイン | 不要 |
| POST | `/auth/logout` | ユーザーログアウト | 必要 |
| POST | `/auth/refresh-token` | アクセストークンの更新 | 不要（リフレッシュトークン必要） |
| POST | `/auth/reset-password/request` | パスワードリセットのリクエスト | 不要 |
| POST | `/auth/reset-password/confirm` | パスワードリセットの確認 | 不要 |

## ログイン

ユーザー認証を行い、JWTトークンを発行します。

### リクエスト

```
POST /auth/login
```

#### リクエストボディ

```json
{
  "email": "user@example.com",
  "password": "your_password"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| email | string | はい | ユーザーのメールアドレス |
| password | string | はい | ユーザーのパスワード |

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "token_type": "Bearer",
    "expires_in": 900,
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "user@example.com",
      "first_name": "Taro",
      "last_name": "Yamada",
      "role": "student"
    }
  },
  "message": "ログインに成功しました"
}
```

| フィールド | 型 | 説明 |
|----------|----|----|
| access_token | string | アクセストークン（有効期限15分） |
| refresh_token | string | リフレッシュトークン（有効期限7日） |
| token_type | string | トークンタイプ（"Bearer"固定） |
| expires_in | number | アクセストークンの有効期限（秒） |
| user | object | ユーザーの基本情報 |

**エラー時 (401 Unauthorized)**

```json
{
  "error": {
    "code": "AUTHENTICATION_ERROR",
    "message": "メールアドレスまたはパスワードが正しくありません"
  }
}
```

## ログアウト

現在のセッションからログアウトし、トークンを無効化します。

### リクエスト

```
POST /auth/logout
```

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (204 No Content)**

レスポンスボディなし

**エラー時 (401 Unauthorized)**

```json
{
  "error": {
    "code": "AUTHENTICATION_ERROR",
    "message": "有効な認証トークンがありません"
  }
}
```

## トークン更新

リフレッシュトークンを使用して新しいアクセストークンを取得します。

### リクエスト

```
POST /auth/refresh-token
```

#### リクエストボディ

```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| refresh_token | string | はい | リフレッシュトークン |

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "token_type": "Bearer",
    "expires_in": 900
  },
  "message": "トークンが更新されました"
}
```

**エラー時 (401 Unauthorized)**

```json
{
  "error": {
    "code": "AUTHENTICATION_ERROR",
    "message": "リフレッシュトークンが無効または期限切れです"
  }
}
```

## パスワードリセットリクエスト

パスワードリセットのメール送信をリクエストします。

### リクエスト

```
POST /auth/reset-password/request
```

#### リクエストボディ

```json
{
  "email": "user@example.com"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| email | string | はい | ユーザーのメールアドレス |

### レスポンス

**成功時 (200 OK)**

```json
{
  "message": "パスワードリセット用のメールを送信しました"
}
```

**注意**: セキュリティ上の理由から、指定されたメールアドレスが存在しない場合も同じレスポンスを返します。

## パスワードリセット確認

リセットトークンを検証し、新しいパスワードを設定します。

### リクエスト

```
POST /auth/reset-password/confirm
```

#### リクエストボディ

```json
{
  "token": "8f7d8937-3728-4c1f-9491-15066e2d419b",
  "password": "new_password",
  "password_confirmation": "new_password"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| token | string | はい | リセットトークン |
| password | string | はい | 新しいパスワード |
| password_confirmation | string | はい | 新しいパスワード（確認用） |

### レスポンス

**成功時 (200 OK)**

```json
{
  "message": "パスワードが正常にリセットされました"
}
```

**エラー時 (400 Bad Request)**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "入力データが無効です",
    "details": [
      {
        "field": "token",
        "message": "トークンが無効または期限切れです"
      }
    ]
  }
}
```

または

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "入力データが無効です",
    "details": [
      {
        "field": "password_confirmation",
        "message": "パスワードと確認用パスワードが一致しません"
      }
    ]
  }
}
```
