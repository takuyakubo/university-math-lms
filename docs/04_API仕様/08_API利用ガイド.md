# API利用ガイド

本ドキュメントでは、大学生向け数学特化型学習管理システム（LMS）のAPIを利用するための基本的な方法と実装例を説明します。

## APIの概要

当システムのAPIは、RESTful原則に基づいて設計されており、JSON形式でデータをやり取りします。APIを通じて以下の操作が可能です：

- ユーザー管理（学生、教員、管理者）
- コース管理
- コンテンツ管理
- 学習進捗の追跡
- 分析データの取得

## 認証方法

### アクセストークンの取得

1. `/api/v1/auth/login` エンドポイントにユーザー認証情報をPOST
2. 返却されたアクセストークンとリフレッシュトークンを保存
3. APIリクエスト時に `Authorization` ヘッダーにトークンを付与

```
Authorization: Bearer {access_token}
```

### トークンの更新

アクセストークンの有効期限（24時間）が切れた場合：

1. `/api/v1/auth/refresh` エンドポイントにリフレッシュトークンをPOST
2. 新しいアクセストークンを取得

## 基本的な利用方法

### リクエスト形式

- GET: リソースの取得
- POST: 新規リソースの作成
- PUT: リソースの更新（全項目）
- PATCH: リソースの部分更新
- DELETE: リソースの削除

### レスポンス形式

すべてのレスポンスは以下の構造を持ちます：

```json
{
  "status": "success" | "error",
  "data": { ... },  // 成功時のレスポンスデータ
  "error": { ... },  // エラー時の詳細情報
  "meta": { ... }  // ページネーション情報など
}
```

### エラーハンドリング

一般的なHTTPステータスコードに加えて、エラー時には詳細情報が提供されます：

```json
{
  "status": "error",
  "error": {
    "code": "ERROR_CODE",
    "message": "人間が読める形式のエラーメッセージ",
    "details": { ... }  // 追加の詳細情報
  }
}
```

## 実装例

### 学生リストの取得（JavaScript）

```javascript
async function getStudents(courseId, page = 1, limit = 20) {
  try {
    const response = await fetch(
      `https://api.math-lms.university.edu/api/v1/courses/${courseId}/students?page=${page}&limit=${limit}`,
      {
        method: 'GET',
        headers: {
          'Authorization': `Bearer ${accessToken}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    const data = await response.json();
    
    if (data.status === 'error') {
      throw new Error(data.error.message);
    }
    
    return data.data;
  } catch (error) {
    console.error('学生リストの取得に失敗しました:', error);
    throw error;
  }
}
```

### 数学課題の提出（Python）

```python
import requests
import json

def submit_math_assignment(assignment_id, student_id, answers):
    url = f"https://api.math-lms.university.edu/api/v1/assignments/{assignment_id}/submissions"
    
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Content-Type": "application/json"
    }
    
    payload = {
        "student_id": student_id,
        "answers": answers
    }
    
    response = requests.post(url, headers=headers, data=json.dumps(payload))
    
    if response.status_code == 201:
        return response.json()['data']
    else:
        error_data = response.json()
        raise Exception(f"提出エラー: {error_data['error']['message']}")
```

## ページネーションの利用

リスト取得系APIはページネーションをサポートしています：

- `page`: ページ番号（デフォルト: 1）
- `limit`: 1ページあたりの項目数（デフォルト: 20、最大: 100）

レスポンスの `meta` オブジェクトに合計ページ数などの情報が含まれます：

```json
{
  "meta": {
    "current_page": 1,
    "total_pages": 5,
    "total_items": 97,
    "items_per_page": 20
  }
}
```

## レート制限

APIの安定性を確保するため、レート制限が設定されています：

- 一般利用者: 100リクエスト/分
- 管理者: 300リクエスト/分

レート制限情報はレスポンスヘッダに含まれます：

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1616036120
```

## API利用のベストプラクティス

1. 必要最小限のデータのみを要求する
2. リクエスト失敗時には適切な再試行戦略を実装する
3. セキュリティのためトークンを安全に保管する
4. 大量データ取得時はページネーションを適切に利用する
5. クエリパラメータでのフィルタリングを活用する
