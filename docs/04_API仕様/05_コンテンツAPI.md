# コンテンツAPI

このドキュメントでは、学習コンテンツ（モジュール、トピック、問題など）に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 | 権限 |
|--------|--------------|------|------|------|
| GET | `/modules/{module_id}` | モジュール詳細の取得 | 必要 | 全ユーザー（登録者/講師/管理者） |
| POST | `/courses/{course_id}/modules` | 新規モジュールの作成 | 必要 | 講師/コンテンツ作成者/管理者 |
| PATCH | `/modules/{module_id}` | モジュール情報の更新 | 必要 | 講師/コンテンツ作成者/管理者 |
| DELETE | `/modules/{module_id}` | モジュールの削除 | 必要 | 講師/管理者 |
| GET | `/topics/{topic_id}` | トピック詳細の取得 | 必要 | 全ユーザー（登録者/講師/管理者） |
| POST | `/modules/{module_id}/topics` | 新規トピックの作成 | 必要 | 講師/コンテンツ作成者/管理者 |
| PATCH | `/topics/{topic_id}` | トピック情報の更新 | 必要 | 講師/コンテンツ作成者/管理者 |
| DELETE | `/topics/{topic_id}` | トピックの削除 | 必要 | 講師/管理者 |
| GET | `/problems/{problem_id}` | 問題詳細の取得 | 必要 | 全ユーザー（登録者/講師/管理者） |
| POST | `/topics/{topic_id}/problems` | 新規問題の作成 | 必要 | 講師/コンテンツ作成者/管理者 |
| PATCH | `/problems/{problem_id}` | 問題情報の更新 | 必要 | 講師/コンテンツ作成者/管理者 |
| DELETE | `/problems/{problem_id}` | 問題の削除 | 必要 | 講師/管理者 |

## モジュール詳細の取得

モジュールID に基づいて特定のモジュール情報を取得します。

### リクエスト

```
GET /modules/{module_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| module_id | string | はい | モジュールの UUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "title": "確率の基礎",
    "description": "確率の基本概念と確率変数について学びます",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "course_title": "確率統計学 - 基礎から応用まで",
    "order_index": 1,
    "status": "published",
    "estimated_hours": 5,
    "topics": [
      {
        "id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
        "title": "確率空間と事象",
        "type": "lesson",
        "order_index": 1,
        "status": "published"
      },
      {
        "id": "b9c0d1e2-f3a4-5678-b9c0-d1e2f3a45678",
        "title": "確率演習問題",
        "type": "quiz",
        "order_index": 3,
        "status": "published"
      }
    ],
    "created_at": "2025-01-20T10:30:00Z",
    "updated_at": "2025-02-15T15:45:30Z"
  }
}
```

## 新規モジュールの作成

コースに新しいモジュールを作成します。

### リクエスト

```
POST /courses/{course_id}/modules
```

#### リクエストボディ

```json
{
  "title": "統計的推定",
  "description": "母集団の特性を標本から推定する方法について学びます",
  "order_index": 3,
  "estimated_hours": 6
}
```

### レスポンス

**成功時 (201 Created)**

```json
{
  "data": {
    "id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "title": "統計的推定",
    "description": "母集団の特性を標本から推定する方法について学びます",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "course_title": "確率統計学 - 基礎から応用まで",
    "order_index": 3,
    "status": "draft",
    "estimated_hours": 6,
    "created_at": "2025-03-11T14:22:33Z"
  },
  "message": "モジュールが正常に作成されました"
}
```

## トピック詳細の取得

トピックID に基づいて特定のトピック情報を取得します。

### リクエスト

```
GET /topics/{topic_id}
```

### レスポンス

**成功時 (200 OK) - レッスンタイプの場合**

```json
{
  "data": {
    "id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
    "title": "確率空間と事象",
    "description": "確率の公理的定義と基本性質について学びます",
    "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "module_title": "確率の基礎",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "type": "lesson",
    "order_index": 1,
    "status": "published",
    "estimated_minutes": 45,
    "content": {
      "sections": [
        {
          "title": "確率の公理",
          "type": "text",
          "content": "確率論は、不確実性を数学的に扱うための理論です..."
        }
      ],
      "resources": [
        {
          "title": "確率空間の図解",
          "type": "image",
          "url": "https://example.com/probability-space-diagram.png"
        }
      ]
    },
    "created_at": "2025-01-25T10:30:00Z",
    "updated_at": "2025-02-15T15:45:30Z"
  }
}
```

## 新規トピックの作成

モジュールに新しいトピックを作成します。

### リクエスト

```
POST /modules/{module_id}/topics
```

#### リクエストボディ

```json
{
  "title": "正規分布と中心極限定理",
  "description": "正規分布の性質と中心極限定理について学びます",
  "type": "lesson",
  "order_index": 2,
  "estimated_minutes": 55,
  "content": {
    "sections": [
      {
        "title": "正規分布の定義",
        "type": "text",
        "content": "正規分布（ガウス分布）は、連続型確率分布の一つで..."
      }
    ]
  }
}
```

## 問題詳細の取得

問題ID に基づいて特定の問題情報を取得します。

### リクエスト

```
GET /problems/{problem_id}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "c0d1e2f3-a4b5-6789-c0d1-e2f3a4b56789",
    "title": "加法定理の応用",
    "topic_id": "b9c0d1e2-f3a4-5678-b9c0-d1e2f3a45678",
    "topic_title": "確率演習問題",
    "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "type": "multiple_choice",
    "difficulty": "medium",
    "points": 10,
    "status": "published",
    "content": {
      "question": "2つのサイコロを振るとき、少なくとも1つが6である確率は？",
      "options": [
        "1/6", "1/3", "11/36", "12/36"
      ],
      "correct_option_index": 2,
      "explanation": "1つ目のサイコロが6である確率は1/6、2つ目のサイコロが6である確率も1/6です..."
    },
    "tags": ["確率", "加法定理", "サイコロ"],
    "created_at": "2025-01-30T14:20:00Z",
    "updated_at": "2025-02-15T15:45:30Z"
  }
}
```

## 新規問題の作成

トピックに新しい問題を作成します。

### リクエスト

```
POST /topics/{topic_id}/problems
```

#### リクエストボディ

```json
{
  "title": "正規分布の性質",
  "type": "multiple_choice",
  "difficulty": "medium",
  "points": 10,
  "content": {
    "question": "標準正規分布 $Z \\sim N(0, 1)$ において、$P(-1 < Z < 1)$ の値として最も近いものはどれか？",
    "options": [
      "約0.50", "約0.68", "約0.95", "約0.99"
    ],
    "correct_option_index": 1,
    "explanation": "標準正規分布では、平均から $\\pm$ 1標準偏差の範囲には約68%の確率が含まれます。"
  },
  "tags": ["正規分布", "確率計算"]
}
```

### レスポンス

**成功時 (201 Created)**

```json
{
  "data": {
    "id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
    "title": "正規分布の性質",
    "topic_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "topic_title": "正規分布と中心極限定理",
    "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "type": "multiple_choice",
    "difficulty": "medium",
    "points": 10,
    "status": "draft",
    "tags": ["正規分布", "確率計算"],
    "created_at": "2025-03-11T15:45:30Z"
  },
  "message": "問題が正常に作成されました"
}
```
