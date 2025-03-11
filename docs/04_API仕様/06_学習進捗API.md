# 学習進捗API

このドキュメントでは、学習進捗の追跡と管理に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 | 権限 |
|--------|--------------|------|------|------|
| GET | `/progress/courses/{course_id}` | コースの進捗状況取得 | 必要 | 学生（本人）/講師/管理者 |
| GET | `/progress/modules/{module_id}` | モジュールの進捗状況取得 | 必要 | 学生（本人）/講師/管理者 |
| POST | `/progress/topics/{topic_id}/complete` | トピックの完了をマーク | 必要 | 学生 |
| POST | `/submissions` | 問題の回答を提出 | 必要 | 学生 |
| GET | `/submissions/{submission_id}` | 提出内容の取得 | 必要 | 学生（本人）/講師/管理者 |
| GET | `/submissions/problems/{problem_id}` | 問題に対する提出一覧取得 | 必要 | 講師/管理者 |
| POST | `/submissions/{submission_id}/grade` | 提出に対する評価を実施 | 必要 | 講師/管理者 |

## コースの進捗状況取得

特定のコースにおけるユーザーの進捗状況を取得します。

### リクエスト

```
GET /progress/courses/{course_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | いいえ | 進捗を表示するユーザーのID（指定なしの場合は現在のユーザー） |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "course_title": "確率統計学 - 基礎から応用まで",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "enrollment_date": "2025-02-15T10:30:00Z",
    "last_activity": "2025-03-10T18:22:33Z",
    "overall_progress": 45.7,
    "modules_progress": [
      {
        "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
        "title": "確率の基礎",
        "progress": 100.0,
        "status": "completed",
        "completed_at": "2025-02-28T15:30:45Z"
      },
      {
        "module_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
        "title": "確率変数と確率分布",
        "progress": 75.0,
        "status": "in_progress",
        "completed_topics": 3,
        "total_topics": 4
      },
      {
        "module_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
        "title": "統計的推定",
        "progress": 0.0,
        "status": "not_started"
      }
    ],
    "assessment_summary": {
      "total_points_earned": 125,
      "total_possible_points": 200,
      "average_score": 62.5,
      "quizzes_completed": 3,
      "quizzes_total": 5
    },
    "time_spent": {
      "total_minutes": 480,
      "last_week_minutes": 120
    }
  }
}
```

## モジュールの進捗状況取得

特定のモジュールにおけるユーザーの進捗状況を取得します。

### リクエスト

```
GET /progress/modules/{module_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| module_id | string | はい | モジュールの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | いいえ | 進捗を表示するユーザーのID（指定なしの場合は現在のユーザー） |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "module_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "module_title": "確率変数と確率分布",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "progress": 75.0,
    "status": "in_progress",
    "topics_progress": [
      {
        "topic_id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
        "title": "離散確率分布",
        "type": "lesson",
        "status": "completed",
        "completed_at": "2025-03-01T14:22:33Z",
        "time_spent_minutes": 35
      },
      {
        "topic_id": "a8b9c0d1-e2f3-4567-a8b9-c0d1e2f34567",
        "title": "連続確率分布",
        "type": "lesson",
        "status": "completed",
        "completed_at": "2025-03-05T16:45:12Z",
        "time_spent_minutes": 42
      },
      {
        "topic_id": "b9c0d1e2-f3a4-5678-b9c0-d1e2f3a45678",
        "title": "正規分布と中心極限定理",
        "type": "lesson",
        "status": "completed",
        "completed_at": "2025-03-08T10:30:45Z",
        "time_spent_minutes": 55
      },
      {
        "topic_id": "c0d1e2f3-a4b5-6789-c0d1-e2f3a4b56789",
        "title": "確率分布演習問題",
        "type": "quiz",
        "status": "not_started"
      }
    ],
    "assessment_summary": {
      "total_points_earned": 0,
      "total_possible_points": 50,
      "quizzes_completed": 0,
      "quizzes_total": 1
    },
    "last_activity": "2025-03-08T10:30:45Z",
    "time_spent": {
      "total_minutes": 132
    }
  }
}
```

## トピックの完了をマーク

学習コンテンツ（レッスン）の完了を記録します。

### リクエスト

```
POST /progress/topics/{topic_id}/complete
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| topic_id | string | はい | トピックの UUID |

#### リクエストボディ

```json
{
  "time_spent_minutes": 45
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| time_spent_minutes | number | いいえ | トピックの学習に費やした時間（分） |

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
    "topic_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "status": "completed",
    "completed_at": "2025-03-11T15:30:45Z",
    "time_spent_minutes": 45,
    "module_progress": 100.0,
    "course_progress": 60.0
  },
  "message": "トピックが正常に完了としてマークされました"
}
```

## 問題の回答を提出

問題に対する回答を提出します。

### リクエスト

```
POST /submissions
```

#### リクエストボディ

```json
{
  "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
  "answer": {
    "selected_option_index": 1
  },
  "time_spent_seconds": 85
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| problem_id | string | はい | 問題の UUID |
| answer | object | はい | 回答内容（問題タイプによって構造が異なる） |
| time_spent_seconds | number | いいえ | 問題の解答に費やした時間（秒） |

**複数選択問題の回答例**

```json
"answer": {
  "selected_option_index": 1
}
```

**短答式問題の回答例**

```json
"answer": {
  "text": "0.68"
}
```

**数式問題の回答例**

```json
"answer": {
  "latex": "\\frac{1}{\\sigma\\sqrt{2\\pi}} e^{-\\frac{(x-\\mu)^2}{2\\sigma^2}}"
}
```

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
    "id": "f3a4b5c6-d7e8-9012-f3a4-b5c6d7e89012",
    "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "submitted_at": "2025-03-11T15:45:30Z",
    "time_spent_seconds": 85,
    "is_correct": true,
    "score": 10,
    "max_score": 10,
    "feedback": {
      "explanation": "正解です！標準正規分布では、平均から $\\pm$ 1標準偏差の範囲には約68%の確率が含まれます。",
      "correct_answer": {
        "selected_option_index": 1
      }
    },
    "attempt_number": 1,
    "topic_progress": 25.0,
    "module_progress": 80.0,
    "course_progress": 62.5
  },
  "message": "回答が正常に提出されました"
}
```

## 提出内容の取得

提出ID に基づいて特定の提出内容を取得します。

### リクエスト

```
GET /submissions/{submission_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| submission_id | string | はい | 提出の UUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "id": "f3a4b5c6-d7e8-9012-f3a4-b5c6d7e89012",
    "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
    "problem_title": "正規分布の性質",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_name": "鈴木 太郎",
    "submitted_at": "2025-03-11T15:45:30Z",
    "time_spent_seconds": 85,
    "is_correct": true,
    "score": 10,
    "max_score": 10,
    "answer": {
      "selected_option_index": 1
    },
    "feedback": {
      "explanation": "正解です！標準正規分布では、平均から $\\pm$ 1標準偏差の範囲には約68%の確率が含まれます。",
      "correct_answer": {
        "selected_option_index": 1
      }
    },
    "graded_by": "system",
    "graded_at": "2025-03-11T15:45:30Z",
    "attempt_number": 1,
    "topic_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234"
  }
}
```

## 問題に対する提出一覧取得

特定の問題に対する全ユーザーの提出内容を取得します。

### リクエスト

```
GET /submissions/problems/{problem_id}
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| problem_id | string | はい | 問題の UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| is_correct | boolean | いいえ | 正解/不正解でフィルタリング |
| needs_grading | boolean | いいえ | 採点が必要な提出のみ取得 |
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
      "id": "f3a4b5c6-d7e8-9012-f3a4-b5c6d7e89012",
      "user_id": "550e8400-e29b-41d4-a716-446655440000",
      "user_name": "鈴木 太郎",
      "submitted_at": "2025-03-11T15:45:30Z",
      "is_correct": true,
      "score": 10,
      "max_score": 10,
      "attempt_number": 1
    },
    {
      "id": "a4b5c6d7-e8f9-0123-a4b5-c6d7e8f90123",
      "user_id": "661f9511-f3ab-52e5-b827-557766551111",
      "user_name": "佐藤 花子",
      "submitted_at": "2025-03-11T14:30:15Z",
      "is_correct": false,
      "score": 0,
      "max_score": 10,
      "attempt_number": 1
    }
  ],
  "meta": {
    "total": 45,
    "limit": 20,
    "offset": 0,
    "next_offset": 20,
    "previous_offset": null,
    "problem_title": "正規分布の性質",
    "average_score": 7.2,
    "correct_rate": 0.65
  }
}
```

## 提出に対する評価を実施

手動採点が必要な提出（エッセイなど）に対して評価を行います。

### リクエスト

```
POST /submissions/{submission_id}/grade
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| submission_id | string | はい | 提出の UUID |

#### リクエストボディ

```json
{
  "score": 12,
  "feedback": {
    "comment": "確率の定義と性質について正確に説明されています。中心極限定理の説明にもう少し数学的な厳密さがあると良いでしょう。",
    "annotation": "中心極限定理は独立同一分布に従う確率変数の平均値の分布に関する定理です。"
  }
}
```

| フィールド | 型 | 必須 | 説明 |
|----------|----|----|-----|
| score | number | はい | 付与するスコア |
| feedback | object | いいえ | フィードバック情報 |
| feedback.comment | string | いいえ | 全体的なコメント |
| feedback.annotation | string | いいえ | 特定の部分に対する注釈 |

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
    "id": "f3a4b5c6-d7e8-9012-f3a4-b5c6d7e89012",
    "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "score": 12,
    "max_score": 15,
    "is_correct": null,
    "graded_by": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
    "graded_at": "2025-03-11T16:30:45Z",
    "feedback": {
      "comment": "確率の定義と性質について正確に説明されています。中心極限定理の説明にもう少し数学的な厳密さがあると良いでしょう。",
      "annotation": "中心極限定理は独立同一分布に従う確率変数の平均値の分布に関する定理です。"
    },
    "topic_progress": 100.0,
    "module_progress": 85.0,
    "course_progress": 65.0
  },
  "message": "提出が正常に評価されました"
}
```
