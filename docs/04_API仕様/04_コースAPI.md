# コースAPI

このドキュメントでは、コース管理に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 | 権限 |
|--------|--------------|------|------|------|
| GET | `/courses` | コース一覧の取得 | 必要 | 全ユーザー |
| GET | `/courses/{course_id}` | 特定コースの詳細取得 | 必要 | 全ユーザー（登録者/講師/管理者） |
| POST | `/courses` | 新規コースの作成 | 必要 | 講師/コンテンツ作成者/管理者 |
| PATCH | `/courses/{course_id}` | コース情報の更新 | 必要 | 講師（作成者）/管理者 |
| DELETE | `/courses/{course_id}` | コースの削除 | 必要 | 管理者 |
| POST | `/courses/{course_id}/publish` | コースの公開 | 必要 | 講師（作成者）/管理者 |
| POST | `/courses/{course_id}/enroll` | コースへの登録 | 必要 | 学生 |
| GET | `/courses/{course_id}/modules` | コースのモジュール一覧取得 | 必要 | 全ユーザー（登録者/講師/管理者） |
| GET | `/courses/{course_id}/students` | コース受講生一覧 | 必要 | 講師/管理者 |

## コース一覧の取得

利用可能なコースの一覧を取得します。

### リクエスト

```
GET /courses
```

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| category | string | いいえ | カテゴリでフィルタリング（例: `calculus`, `linear_algebra`） |
| status | string | いいえ | ステータスでフィルタリング (`published`, `draft`) |
| search | string | いいえ | タイトルまたは説明で検索 |
| creator_id | string | いいえ | 作成者IDでフィルタリング |
| difficulty | string | いいえ | 難易度でフィルタリング (`beginner`, `intermediate`, `advanced`) |
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
      "thumbnail_url": "https://example.com/calculus-thumbnail.jpg",
      "category": "calculus",
      "difficulty": "beginner",
      "status": "published",
      "creator": {
        "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
        "name": "田中 教授"
      },
      "stats": {
        "students_count": 156,
        "modules_count": 8,
        "average_rating": 4.7
      },
      "created_at": "2025-01-15T00:00:00Z",
      "updated_at": "2025-02-10T12:30:45Z"
    },
    {
      "id": "b2c3d4e5-f6a7-8901-b2c3-d4e5f6a78901",
      "title": "線形代数学",
      "description": "ベクトルと行列の基礎から応用まで",
      "thumbnail_url": "https://example.com/linear-algebra-thumbnail.jpg",
      "category": "linear_algebra",
      "difficulty": "intermediate",
      "status": "published",
      "creator": {
        "id": "7ca8b911-9dad-11d1-80b4-00c04fd430c8",
        "name": "佐藤 准教授"
      },
      "stats": {
        "students_count": 142,
        "modules_count": 10,
        "average_rating": 4.5
      },
      "created_at": "2025-01-20T00:00:00Z",
      "updated_at": "2025-02-15T10:20:30Z"
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

## 特定コースの詳細取得

コースIDに基づいて特定のコース情報を取得します。

### リクエスト

```
GET /courses/{course_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "a1b2c3d4-e5f6-7890-a1b2-c3d4e5f67890",
    "title": "微分積分学I",
    "description": "大学1年生向けの基礎微積分コース",
    "full_description": "本コースでは、微分積分学の基本概念から応用までを学びます。極限、連続性、微分法、積分法の基礎を丁寧に解説し、様々な応用例を通じて理解を深めていきます。",
    "thumbnail_url": "https://example.com/calculus-thumbnail.jpg",
    "category": "calculus",
    "difficulty": "beginner",
    "status": "published",
    "prerequisites": ["高校数学III", "基礎解析"],
    "learning_objectives": [
      "極限と連続性の概念を理解する",
      "導関数の意味と計算方法を習得する",
      "不定積分と定積分の計算ができる",
      "微分積分学の応用問題を解けるようになる"
    ],
    "creator": {
      "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
      "name": "田中 教授",
      "email": "tanaka@example.university.ac.jp",
      "profile_image": "https://example.com/tanaka-profile.jpg"
    },
    "stats": {
      "students_count": 156,
      "modules_count": 8,
      "topics_count": 32,
      "estimated_hours": 45,
      "average_rating": 4.7,
      "completion_rate": 78.5
    },
    "modules": [
      {
        "id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
        "title": "関数と極限",
        "order_index": 1,
        "topics_count": 4
      },
      {
        "id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
        "title": "微分法の基礎",
        "order_index": 2,
        "topics_count": 5
      }
      // 他のモジュール...
    ],
    "created_at": "2025-01-15T00:00:00Z",
    "updated_at": "2025-02-10T12:30:45Z",
    "published_at": "2025-02-10T12:30:45Z"
  }
}
```

**エラー時 (404 Not Found)**

```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "指定されたコースは存在しません"
  }
}
```

## 新規コースの作成

新しいコースを作成します。

### リクエスト

```
POST /courses
```

#### リクエストボディ

```json
{
  "title": "確率統計学",
  "description": "データサイエンスの基礎となる確率と統計の理論",
  "full_description": "本コースでは、確率論と統計学の基礎から応用までを学びます。確率変数、確率分布、推定、検定などの基本概念を習得し、データ分析の基礎を固めることを目指します。",
  "category": "statistics",
  "difficulty": "intermediate",
  "prerequisites": ["微分積分学I", "線形代数学"],
  "learning_objectives": [
    "確率変数と確率分布の概念を理解する",
    "統計的推定と検定の方法を習得する",
    "確率モデルを用いた分析手法を学ぶ"
  ],
  "thumbnail_url": "https://example.com/statistics-thumbnail.jpg"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| title | string | はい | コースのタイトル |
| description | string | はい | コースの簡単な説明 |
| full_description | string | いいえ | コースの詳細な説明 |
| category | string | はい | コースカテゴリ |
| difficulty | string | はい | 難易度 (`beginner`, `intermediate`, `advanced`) |
| prerequisites | array | いいえ | 前提条件となるコースや知識 |
| learning_objectives | array | いいえ | 学習目標 |
| thumbnail_url | string | いいえ | サムネイル画像のURL |

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
    "id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "title": "確率統計学",
    "description": "データサイエンスの基礎となる確率と統計の理論",
    "full_description": "本コースでは、確率論と統計学の基礎から応用までを学びます。確率変数、確率分布、推定、検定などの基本概念を習得し、データ分析の基礎を固めることを目指します。",
    "thumbnail_url": "https://example.com/statistics-thumbnail.jpg",
    "category": "statistics",
    "difficulty": "intermediate",
    "status": "draft",
    "prerequisites": ["微分積分学I", "線形代数学"],
    "learning_objectives": [
      "確率変数と確率分布の概念を理解する",
      "統計的推定と検定の方法を習得する",
      "確率モデルを用いた分析手法を学ぶ"
    ],
    "creator": {
      "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
      "name": "田中 教授"
    },
    "created_at": "2025-03-11T14:22:33Z",
    "updated_at": "2025-03-11T14:22:33Z"
  },
  "message": "コースが正常に作成されました"
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
        "field": "title",
        "message": "タイトルは必須です"
      }
    ]
  }
}
```

## コース情報の更新

特定のコース情報を更新します。

### リクエスト

```
PATCH /courses/{course_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

#### リクエストボディ

```json
{
  "title": "確率統計学 - 基礎から応用まで",
  "learning_objectives": [
    "確率変数と確率分布の概念を理解する",
    "統計的推定と検定の方法を習得する",
    "確率モデルを用いた分析手法を学ぶ",
    "Rを用いたデータ分析の基礎を習得する"
  ],
  "difficulty": "intermediate"
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| title | string | いいえ | コースのタイトル |
| description | string | いいえ | コースの簡単な説明 |
| full_description | string | いいえ | コースの詳細な説明 |
| category | string | いいえ | コースカテゴリ |
| difficulty | string | いいえ | 難易度 |
| prerequisites | array | いいえ | 前提条件となるコースや知識 |
| learning_objectives | array | いいえ | 学習目標 |
| thumbnail_url | string | いいえ | サムネイル画像のURL |

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
    "id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "title": "確率統計学 - 基礎から応用まで",
    "description": "データサイエンスの基礎となる確率と統計の理論",
    "full_description": "本コースでは、確率論と統計学の基礎から応用までを学びます。確率変数、確率分布、推定、検定などの基本概念を習得し、データ分析の基礎を固めることを目指します。",
    "thumbnail_url": "https://example.com/statistics-thumbnail.jpg",
    "category": "statistics",
    "difficulty": "intermediate",
    "status": "draft",
    "prerequisites": ["微分積分学I", "線形代数学"],
    "learning_objectives": [
      "確率変数と確率分布の概念を理解する",
      "統計的推定と検定の方法を習得する",
      "確率モデルを用いた分析手法を学ぶ",
      "Rを用いたデータ分析の基礎を習得する"
    ],
    "creator": {
      "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
      "name": "田中 教授"
    },
    "updated_at": "2025-03-11T15:30:45Z"
  },
  "message": "コース情報が正常に更新されました"
}
```

## コースの公開

ドラフト状態のコースを公開します。

### リクエスト

```
POST /courses/{course_id}/publish
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "title": "確率統計学 - 基礎から応用まで",
    "status": "published",
    "published_at": "2025-03-11T16:00:00Z"
  },
  "message": "コースが正常に公開されました"
}
```

**エラー時 (422 Unprocessable Entity)**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "コースを公開できません",
    "details": [
      {
        "message": "コースには少なくとも1つのモジュールが必要です"
      }
    ]
  }
}
```

## コースへの登録

学生がコースに登録します。

### リクエスト

```
POST /courses/{course_id}/enroll
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "enrollment_id": "f6a7b8c9-d0e1-2345-f6a7-b8c9d0e12345",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "course_title": "確率統計学 - 基礎から応用まで",
    "enrollment_date": "2025-03-11T16:30:00Z",
    "status": "enrolled"
  },
  "message": "コースに正常に登録されました"
}
```

**エラー時 (409 Conflict)**

```json
{
  "error": {
    "code": "ALREADY_ENROLLED",
    "message": "すでにこのコースに登録されています"
  }
}
```

## コースのモジュール一覧取得

コースに含まれるモジュールの一覧を取得します。

### リクエスト

```
GET /courses/{course_id}/modules
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

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
      "id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
      "title": "確率の基礎",
      "description": "確率の基本概念と確率変数について学びます",
      "order_index": 1,
      "topics_count": 4,
      "status": "published",
      "estimated_hours": 5,
      "topics": [
        {
          "id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
          "title": "確率空間と事象",
          "type": "lesson",
          "order_index": 1
        },
        {
          "id": "a8b9c0d1-e2f3-4567-a8b9-c0d1e2f34567",
          "title": "条件付き確率と独立性",
          "type": "lesson",
          "order_index": 2
        }
        // その他のトピック...
      ]
    },
    {
      "id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
      "title": "確率分布",
      "description": "離散型と連続型の確率分布について学びます",
      "order_index": 2,
      "topics_count": 5,
      "status": "published",
      "estimated_hours": 6,
      "topics": [
        // トピックの配列...
      ]
    }
    // その他のモジュール...
  ],
  "meta": {
    "total": 8,
    "course_title": "確率統計学 - 基礎から応用まで"
  }
}
```

## コース受講生一覧

コースに登録している学生の一覧を取得します。

### リクエスト

```
GET /courses/{course_id}/students
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースのUUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| status | string | いいえ | ステータスでフィルタリング (`enrolled`, `completed`, `dropped`) |
| search | string | いいえ | 学生名またはメールアドレスで検索 |
| sort | string | いいえ | ソート基準 (`enrollment_date`, `progress`, `last_activity`) |
| order | string | いいえ | ソート順 (`asc`, `desc`, デフォルト: `desc`) |
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
      "id": "g7b8c9d0-e1f2-3456-g7b8-c9d0e1f23456",
      "user_id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "鈴木 太郎",
      "email": "suzuki@student.example.ac.jp",
      "enrollment_date": "2025-02-15T10:30:00Z",
      "status": "enrolled",
      "progress": 45.7,
      "last_activity": "2025-03-10T18:22:33Z",
      "completed_topics": 16,
      "total_topics": 35
    },
    {
      "id": "h8c9d0e1-f2g3-4567-h8c9-d0e1f2g34567",
      "user_id": "661f9511-f3ab-52e5-b827-557766551111",
      "name": "佐藤 花子",
      "email": "sato@student.example.ac.jp",
      "enrollment_date": "2025-02-16T09:15:00Z",
      "status": "enrolled",
      "progress": 62.8,
      "last_activity": "2025-03-11T08:45:12Z",
      "completed_topics": 22,
      "total_topics": 35
    }
    // その他の学生...
  ],
  "meta": {
    "total": 156,
    "limit": 20,
    "offset": 0,
    "next_offset": 20,
    "previous_offset": null,
    "course_title": "確率統計学 - 基礎から応用まで"
  }
}
```
