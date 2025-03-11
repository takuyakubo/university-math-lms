# ユーザーAPI

このドキュメントでは、ユーザー管理に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 | 権限 |
|--------|--------------|------|------|------|
| GET | `/users` | ユーザー一覧の取得 | 必要 | 管理者/講師 |
| GET | `/users/{user_id}` | 特定ユーザーの詳細取得 | 必要 | 管理者/本人 |
| POST | `/users` | 新規ユーザーの作成 | 必要 | 管理者 |
| PATCH | `/users/{user_id}` | ユーザー情報の更新 | 必要 | 管理者/本人 |
| DELETE | `/users/{user_id}` | ユーザーの削除 | 必要 | 管理者 |
| GET | `/users/me` | 現在認証中のユーザー情報取得 | 必要 | 全ユーザー |
| PATCH | `/users/me/password` | 自分のパスワード変更 | 必要 | 全ユーザー |
| GET | `/users/{user_id}/courses` | ユーザーが登録しているコース一覧 | 必要 | 管理者/講師/本人 |

## ユーザー一覧の取得

登録されているユーザーの一覧を取得します。

### リクエスト

```
GET /users
```

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| role | string | いいえ | ロールでフィルタリング (`student`, `instructor`, `content_creator`, `admin`) |
| status | string | いいえ | ステータスでフィルタリング (`active`, `inactive`) |
| search | string | いいえ | 名前またはメールアドレスで検索 |
| limit | number | いいえ | 1ページあたりの件数（デフォルト: 20） |
| offset | number | いいえ | 取得開始位置（デフォルト: 0） |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "user1@example.com",
      "first_name": "Taro",
      "last_name": "Yamada",
      "role": "student",
      "status": "active",
      "created_at": "2025-01-01T00:00:00Z",
      "last_login": "2025-03-01T12:30:45Z"
    },
    {
      "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
      "email": "user2@example.com",
      "first_name": "Hanako",
      "last_name": "Suzuki",
      "role": "instructor",
      "status": "active",
      "created_at": "2025-01-05T10:20:30Z",
      "last_login": "2025-03-10T08:15:22Z"
    }
  ],
  "meta": {
    "total": 45,
    "limit": 20,
    "offset": 0,
    "next_offset": 20,
    "previous_offset": null
  }
}
```

**エラー時 (403 Forbidden)**

```json
{
  "error": {
    "code": "AUTHORIZATION_ERROR",
    "message": "このリソースにアクセスする権限がありません"
  }
}
```

## 特定ユーザーの詳細取得

ユーザーIDに基づいて特定のユーザー情報を取得します。

### リクエスト

```
GET /users/{user_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | はい | ユーザーのUUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "first_name": "Taro",
    "last_name": "Yamada",
    "role": "student",
    "status": "active",
    "profile": {
      "avatar_url": "https://example.com/avatar.jpg",
      "bio": "数学専攻の大学3年生です",
      "preferences": {
        "notifications": {
          "email": true,
          "push": false
        },
        "theme": "light"
      }
    },
    "created_at": "2025-01-01T00:00:00Z",
    "last_login": "2025-03-01T12:30:45Z"
  }
}
```

**エラー時 (404 Not Found)**

```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "指定されたユーザーは存在しません"
  }
}
```

## 新規ユーザーの作成

新しいユーザーを作成します。

### リクエスト

```
POST /users
```

#### リクエストボディ

```json
{
  "email": "new_user@example.com",
  "password": "securepassword123",
  "first_name": "Ichiro",
  "last_name": "Tanaka",
  "role": "student",
  "status": "active"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| email | string | はい | ユーザーのメールアドレス |
| password | string | はい | ユーザーの初期パスワード |
| first_name | string | はい | 名 |
| last_name | string | はい | 姓 |
| role | string | はい | ユーザーロール (`student`, `instructor`, `content_creator`, `admin`) |
| status | string | いいえ | アカウント状態（デフォルト: `active`） |

#### ヘッダー

```
Authorization: Bearer {access_token}
Content-Type: application/json
```

### レスポンス

**成功時 (201 Created)**

```json
{
  "data": {
    "id": "8b3c1ef9-5a2d-4b4f-a8c7-9f1e3d2b0c8a",
    "email": "new_user@example.com",
    "first_name": "Ichiro",
    "last_name": "Tanaka",
    "role": "student",
    "status": "active",
    "created_at": "2025-03-11T14:22:33Z"
  },
  "message": "ユーザーが正常に作成されました"
}
```

**エラー時 (422 Unprocessable Entity)**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "入力データが無効です",
    "details": [
      {
        "field": "email",
        "message": "このメールアドレスは既に使用されています"
      }
    ]
  }
}
```

## ユーザー情報の更新

特定のユーザー情報を更新します。

### リクエスト

```
PATCH /users/{user_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | はい | ユーザーのUUID |

#### リクエストボディ

```json
{
  "first_name": "Jiro",
  "status": "inactive",
  "profile": {
    "bio": "数学専攻の大学4年生です",
    "preferences": {
      "notifications": {
        "email": false
      }
    }
  }
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| email | string | いいえ | ユーザーのメールアドレス |
| first_name | string | いいえ | 名 |
| last_name | string | いいえ | 姓 |
| role | string | いいえ | ユーザーロール |
| status | string | いいえ | アカウント状態 |
| profile | object | いいえ | ユーザープロフィール情報 |

#### ヘッダー

```
Authorization: Bearer {access_token}
Content-Type: application/json
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "first_name": "Jiro",
    "last_name": "Yamada",
    "role": "student",
    "status": "inactive",
    "profile": {
      "avatar_url": "https://example.com/avatar.jpg",
      "bio": "数学専攻の大学4年生です",
      "preferences": {
        "notifications": {
          "email": false,
          "push": false
        },
        "theme": "light"
      }
    },
    "updated_at": "2025-03-11T14:30:45Z"
  },
  "message": "ユーザー情報が正常に更新されました"
}
```

## 現在認証中のユーザー情報取得

現在ログインしているユーザー自身の情報を取得します。

### リクエスト

```
GET /users/me
```

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "first_name": "Taro",
    "last_name": "Yamada",
    "role": "student",
    "status": "active",
    "profile": {
      "avatar_url": "https://example.com/avatar.jpg",
      "bio": "数学専攻の大学3年生です",
      "preferences": {
        "notifications": {
          "email": true,
          "push": false
        },
        "theme": "light"
      }
    },
    "created_at": "2025-01-01T00:00:00Z",
    "last_login": "2025-03-11T12:30:45Z",
    "courses_enrolled": 5,
    "progress_summary": {
      "completed_courses": 2,
      "in_progress_courses": 3,
      "average_score": 85.5
    }
  }
}
```

## ユーザーが登録しているコース一覧

ユーザーが登録しているコースの一覧を取得します。

### リクエスト

```
GET /users/{user_id}/courses
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | はい | ユーザーのUUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| status | string | いいえ | コース状態でフィルタリング (`enrolled`, `completed`, `in_progress`) |
| limit | number | いいえ | 1ページあたりの件数（デフォルト: 20） |
| offset | number | いいえ | 取得開始位置（デフォルト: 0） |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": [
    {
      "id": "a1b2c3d4-e5f6-7890-a1b2-c3d4e5f67890",
      "title": "微分積分学I",
      "description": "大学1年生向けの基礎微積分コース",
      "enrollment_date": "2025-01-15T00:00:00Z",
      "status": "in_progress",
      "progress": 45,
      "last_accessed": "2025-03-10T14:22:33Z"
    },
    {
      "id": "b2c3d4e5-f6a7-8901-b2c3-d4e5f6a78901",
      "title": "線形代数学",
      "description": "ベクトルと行列の基礎から応用まで",
      "enrollment_date": "2025-02-01T00:00:00Z",
      "status": "in_progress",
      "progress": 30,
      "last_accessed": "2025-03-09T10:15:20Z"
    }
  ],
  "meta": {
    "total": 5,
    "limit": 20,
    "offset": 0,
    "next_offset": null,
    "previous_offset": null
  }
}
```
