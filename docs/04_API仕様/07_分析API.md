# 分析API

このドキュメントでは、学習データの分析と統計に関連するAPI仕様を定義します。

## エンドポイント一覧

| メソッド | エンドポイント | 説明 | 認証 | 権限 |
|--------|--------------|------|------|------|
| GET | `/analytics/courses/{course_id}/summary` | コース全体の分析サマリー取得 | 必要 | 講師/管理者 |
| GET | `/analytics/courses/{course_id}/students` | コース受講生の進捗分析取得 | 必要 | 講師/管理者 |
| GET | `/analytics/courses/{course_id}/content` | コースコンテンツの利用状況分析取得 | 必要 | 講師/管理者 |
| GET | `/analytics/problems/{problem_id}/stats` | 問題の解答統計取得 | 必要 | 講師/管理者 |
| GET | `/analytics/users/{user_id}/performance` | ユーザーの学習パフォーマンス分析取得 | 必要 | 学生（本人）/講師/管理者 |
| GET | `/analytics/users/{user_id}/recommendations` | 学習推奨コンテンツの取得 | 必要 | 学生（本人）/講師/管理者 |

## コース全体の分析サマリー取得

コース全体の統計と分析情報を取得します。

### リクエスト

```
GET /analytics/courses/{course_id}/summary
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| start_date | string | いいえ | 期間の開始日（ISO 8601形式） |
| end_date | string | いいえ | 期間の終了日（ISO 8601形式） |

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
    "period": {
      "start_date": "2025-01-01T00:00:00Z",
      "end_date": "2025-03-11T00:00:00Z"
    },
    "enrollment_stats": {
      "total_students": 156,
      "active_students": 142,
      "completion_rate": 35.2,
      "dropout_rate": 8.9,
      "new_enrollments": 15
    },
    "engagement_stats": {
      "average_time_spent_hours": 24.5,
      "average_sessions_per_week": 3.2,
      "average_session_duration_minutes": 42.7
    },
    "performance_stats": {
      "average_score": 76.3,
      "pass_rate": 88.5,
      "average_attempts_per_problem": 1.4,
      "topics_with_lowest_completion": [
        {
          "topic_id": "c0d1e2f3-a4b5-6789-c0d1-e2f3a4b56789",
          "title": "確率分布演習問題",
          "completion_rate": 45.2
        },
        {
          "topic_id": "d1e2f3a4-b5c6-7890-d1e2-f3a4b5c67890",
          "title": "ベイズ統計学入門",
          "completion_rate": 52.8
        }
      ],
      "problems_with_lowest_success": [
        {
          "problem_id": "a4b5c6d7-e8f9-0123-a4b5-c6d7e8f90123",
          "title": "ベイズの定理の応用",
          "success_rate": 42.1
        }
      ]
    },
    "time_distribution": {
      "by_day_of_week": [
        { "day": "Monday", "minutes": 8520 },
        { "day": "Tuesday", "minutes": 9450 },
        { "day": "Wednesday", "minutes": 7890 },
        { "day": "Thursday", "minutes": 8760 },
        { "day": "Friday", "minutes": 6540 },
        { "day": "Saturday", "minutes": 4320 },
        { "day": "Sunday", "minutes": 5670 }
      ],
      "by_hour_of_day": [
        { "hour": 9, "minutes": 4230 },
        { "hour": 10, "minutes": 5670 },
        { "hour": 22, "minutes": 7890 }
      ]
    }
  }
}
```

## コース受講生の進捗分析取得

コースに登録している学生の進捗と学習パターンの分析を取得します。

### リクエスト

```
GET /analytics/courses/{course_id}/students
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| sort_by | string | いいえ | ソート基準 (`progress`, `score`, `time_spent`, `last_activity`) |
| sort_order | string | いいえ | ソート順 (`asc`, `desc`, デフォルト: `desc`) |
| progress_min | number | いいえ | 最小進捗率でフィルタリング（%） |
| progress_max | number | いいえ | 最大進捗率でフィルタリング（%） |
| active_within_days | number | いいえ | 直近N日間でアクティブな学生のみ |
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
  "data": {
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "course_title": "確率統計学 - 基礎から応用まで",
    "students": [
      {
        "user_id": "550e8400-e29b-41d4-a716-446655440000",
        "name": "鈴木 太郎",
        "email": "suzuki@student.example.ac.jp",
        "progress": 82.5,
        "status": "in_progress",
        "average_score": 88.7,
        "time_spent_hours": 32.5,
        "last_activity": "2025-03-10T18:22:33Z",
        "engagement_level": "high",
        "strengths": ["確率基礎", "分布関数"],
        "weaknesses": ["推定理論", "検定"],
        "risk_level": "low"
      },
      {
        "user_id": "661f9511-f3ab-52e5-b827-557766551111",
        "name": "佐藤 花子",
        "email": "sato@student.example.ac.jp",
        "progress": 45.2,
        "status": "behind",
        "average_score": 65.3,
        "time_spent_hours": 18.7,
        "last_activity": "2025-03-01T09:15:20Z",
        "engagement_level": "medium",
        "strengths": ["記述統計", "グラフ解釈"],
        "weaknesses": ["確率計算", "ベイズ統計"],
        "risk_level": "medium"
      }
    ],
    "clusters": [
      {
        "name": "高成績・高進捗グループ",
        "count": 42,
        "average_progress": 85.3,
        "average_score": 92.1
      },
      {
        "name": "中成績・中進捗グループ",
        "count": 89,
        "average_progress": 52.4,
        "average_score": 74.3
      },
      {
        "name": "低成績・低進捗グループ",
        "count": 25,
        "average_progress": 28.7,
        "average_score": 61.2
      }
    ],
    "meta": {
      "total": 156,
      "limit": 20,
      "offset": 0,
      "next_offset": 20,
      "previous_offset": null
    }
  }
}
```

## コースコンテンツの利用状況分析取得

コース内のコンテンツ（モジュール、トピック、問題など）の利用状況を分析します。

### リクエスト

```
GET /analytics/courses/{course_id}/content
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | はい | コースの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| content_type | string | いいえ | コンテンツタイプでフィルタリング (`modules`, `topics`, `problems`) |
| sort_by | string | いいえ | ソート基準 (`views`, `completion_rate`, `average_time`, `difficulty`) |
| sort_order | string | いいえ | ソート順 (`asc`, `desc`, デフォルト: `desc`) |

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
    "modules": [
      {
        "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
        "title": "確率の基礎",
        "views": 156,
        "completion_rate": 92.3,
        "average_time_minutes": 245,
        "difficulty_rating": 2.4
      },
      {
        "module_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
        "title": "確率変数と確率分布",
        "views": 148,
        "completion_rate": 78.5,
        "average_time_minutes": 320,
        "difficulty_rating": 3.2
      }
    ],
    "topics": [
      {
        "topic_id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
        "title": "確率空間と事象",
        "type": "lesson",
        "views": 156,
        "completion_rate": 95.5,
        "average_time_minutes": 42,
        "difficulty_rating": 2.2
      },
      {
        "topic_id": "c0d1e2f3-a4b5-6789-c0d1-e2f3a4b56789",
        "title": "確率分布演習問題",
        "type": "quiz",
        "views": 135,
        "completion_rate": 45.2,
        "average_time_minutes": 68,
        "difficulty_rating": 4.1,
        "average_score": 72.5
      }
    ],
    "problems": [
      {
        "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
        "title": "正規分布の性質",
        "type": "multiple_choice",
        "views": 135,
        "attempts": 142,
        "success_rate": 78.2,
        "average_time_seconds": 85,
        "common_mistakes": [
          {
            "option_index": 2,
            "frequency": 15
          }
        ]
      }
    ],
    "content_correlations": [
      {
        "source_id": "f7a8b9c0-d1e2-3456-f7a8-b9c0d1e23456",
        "target_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
        "correlation_type": "performance",
        "correlation_strength": 0.82,
        "description": "確率空間と事象の理解度が高い学生は正規分布の性質の問題の正答率が高い"
      }
    ]
  }
}
```

## 問題の解答統計取得

特定の問題に関する解答統計と分析を取得します。

### リクエスト

```
GET /analytics/problems/{problem_id}/stats
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| problem_id | string | はい | 問題の UUID |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "problem_id": "e2f3a4b5-c6d7-8901-e2f3-a4b5c6d78901",
    "problem_title": "正規分布の性質",
    "problem_type": "multiple_choice",
    "topic_id": "d4e5f6a7-b8c9-0123-d4e5-f6a7b8c90123",
    "module_id": "c3d4e5f6-a7b8-9012-c3d4-e5f6a7b89012",
    "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
    "summary": {
      "total_attempts": 142,
      "unique_students": 135,
      "success_rate": 78.2,
      "average_time_seconds": 85,
      "average_attempts": 1.05,
      "difficulty_rating": 3.2
    },
    "answer_distribution": {
      "options": [
        {
          "index": 0,
          "text": "約0.50",
          "count": 12,
          "percentage": 8.5
        },
        {
          "index": 1,
          "text": "約0.68",
          "count": 111,
          "percentage": 78.2,
          "is_correct": true
        },
        {
          "index": 2,
          "text": "約0.95",
          "count": 15,
          "percentage": 10.6
        },
        {
          "index": 3,
          "text": "約0.99",
          "count": 4,
          "percentage": 2.8
        }
      ]
    },
    "time_distribution": {
      "buckets": [
        { "range": "0-30秒", "count": 25 },
        { "range": "31-60秒", "count": 45 },
        { "range": "61-90秒", "count": 52 },
        { "range": "91-120秒", "count": 15 },
        { "range": "120秒以上", "count": 5 }
      ]
    },
    "performance_factors": [
      {
        "factor": "正規分布と中心極限定理トピックの完了",
        "correlation": 0.75,
        "description": "正規分布と中心極限定理のトピックを完了している学生は、この問題の正答率が高い傾向がある"
      }
    ]
  }
}
```

## ユーザーの学習パフォーマンス分析取得

特定のユーザーの学習パフォーマンスに関する分析を取得します。

### リクエスト

```
GET /analytics/users/{user_id}/performance
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | はい | ユーザーの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | いいえ | 分析対象のコース（指定なしの場合はすべてのコース） |
| period | string | いいえ | 期間 (`week`, `month`, `quarter`, `year`, `all`) |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_name": "鈴木 太郎",
    "period": {
      "start_date": "2025-01-01T00:00:00Z",
      "end_date": "2025-03-11T00:00:00Z",
      "type": "quarter"
    },
    "overview": {
      "total_courses_enrolled": 3,
      "courses_completed": 1,
      "average_progress": 68.5,
      "average_score": 82.3,
      "total_time_spent_hours": 87.5,
      "average_sessions_per_week": 4.2
    },
    "course_performance": [
      {
        "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
        "course_title": "確率統計学 - 基礎から応用まで",
        "progress": 82.5,
        "average_score": 88.7,
        "time_spent_hours": 32.5,
        "rank_percentile": 85
      }
    ],
    "knowledge_areas": {
      "strengths": [
        {
          "area": "確率基礎",
          "score": 95.2,
          "topics": ["確率空間と事象", "条件付き確率"]
        },
        {
          "area": "分布関数",
          "score": 91.5,
          "topics": ["確率密度関数", "連続確率分布"]
        }
      ],
      "weaknesses": [
        {
          "area": "推定理論",
          "score": 68.3,
          "topics": ["点推定", "区間推定"],
          "recommendation": "推定理論の基礎に関する復習が推奨されます"
        }
      ]
    },
    "learning_patterns": {
      "preferred_time": {
        "day_of_week": "Tuesday",
        "hour_of_day": 20
      },
      "session_length": {
        "average_minutes": 45,
        "preferred_length": "30-60分"
      },
      "content_preference": {
        "preferred_content_type": "interactive",
        "engagement_by_type": {
          "video": 0.6,
          "text": 0.8,
          "interactive": 0.9,
          "quiz": 0.75
        }
      }
    },
    "progress_over_time": {
      "weekly_progress": [
        { "week": "2025-01-01", "progress": 5.2 },
        { "week": "2025-01-08", "progress": 12.8 },
        // 省略
        { "week": "2025-03-05", "progress": 82.5 }
      ],
      "milestone_achievements": [
        {
          "date": "2025-02-15T15:30:45Z",
          "milestone": "確率の基礎モジュール完了",
          "related_skill": "確率計算"
        }
      ]
    }
  }
}
```

## 学習推奨コンテンツの取得

ユーザーの学習パターンとパフォーマンスに基づいて推奨コンテンツを取得します。

### リクエスト

```
GET /analytics/users/{user_id}/recommendations
```

#### パスパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| user_id | string | はい | ユーザーの UUID |

#### クエリパラメータ

| パラメータ | 型 | 必須 | 説明 |
|----------|----|----|-----|
| course_id | string | いいえ | コンテキストとなるコース（指定なしの場合は全体から推奨） |
| type | string | いいえ | 推奨タイプ (`next_step`, `review`, `practice`, `extension`) |
| limit | number | いいえ | 取得件数（デフォルト: 5） |

#### ヘッダー

```
Authorization: Bearer {access_token}
```

### レスポンス

**成功時 (200 OK)**

```json
{
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "recommendations": [
      {
        "type": "next_step",
        "content_type": "topic",
        "content_id": "d1e2f3a4-b5c6-7890-d1e2-f3a4b5c67890",
        "title": "ベイズ統計学入門",
        "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
        "course_title": "確率統計学 - 基礎から応用まで",
        "reason": "現在の学習の自然な次のステップです",
        "relevance_score": 0.95
      },
      {
        "type": "review",
        "content_type": "topic",
        "content_id": "c0d1e2f3-a4b5-6789-c0d1-e2f3a4b56789",
        "title": "確率分布演習問題",
        "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
        "course_title": "確率統計学 - 基礎から応用まで",
        "reason": "この分野の問題で成績が低めです。復習が推奨されます",
        "relevance_score": 0.85
      },
      {
        "type": "practice",
        "content_type": "problem",
        "content_id": "a4b5c6d7-e8f9-0123-a4b5-c6d7e8f90123",
        "title": "ベイズの定理の応用",
        "course_id": "e5f6a7b8-c9d0-1234-e5f6-a7b8c9d01234",
        "course_title": "確率統計学 - 基礎から応用まで",
        "reason": "この概念の理解を深めるための追加練習です",
        "relevance_score": 0.82
      },
      {
        "type": "extension",
        "content_type": "course",
        "content_id": "f6a7b8c9-d0e1-2345-f6a7-b8c9d0e12345",
        "title": "データサイエンス入門",
        "reason": "現在学習中の概念を応用したコースです",
        "relevance_score": 0.75
      }
    ],
    "learning_path": {
      "current_focus": "確率分布と推測統計",
      "next_topics": [
        "ベイズ統計学入門",
        "統計的仮説検定",
        "回帰分析の基礎"
      ],
      "estimated_completion": "2025-05-15",
      "skill_gaps": [
        {
          "skill": "ベイズ理論",
          "gap_level": "medium",
          "recommended_resources": ["ベイズ統計学入門", "条件付き確率の応用"]
        }
      ]
    }
  }
}
```
