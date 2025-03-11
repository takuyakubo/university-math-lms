# API概要

このドキュメントでは、大学生向け数学特化型学習管理システム（LMS）のAPI全体像と共通仕様について説明します。

## API基本情報

### ベースURL

```
https://api.university-math-lms.example.com/v1
```

### API構成

APIは以下の主要カテゴリに分類されます：

1. **認証API**: ユーザー認証とトークン管理
2. **ユーザーAPI**: ユーザー情報の管理
3. **コースAPI**: コースとモジュールの管理
4. **コンテンツAPI**: 学習コンテンツと問題の管理
5. **学習進捗API**: 学習状況と成績の追跡
6. **分析API**: 学習データの分析と統計

## 共通仕様

### リクエストフォーマット

- **Content-Type**: `application/json`
- エンコーディング: UTF-8
- 日時フォーマット: ISO 8601 (`YYYY-MM-DDThh:mm:ssZ`)

### 認証

- JWT (JSON Web Token) を使用した認証
- リクエストヘッダに `Authorization: Bearer {token}` 形式でトークンを含める
- 認証が必要なエンドポイントは仕様に明記

### レスポンスフォーマット

すべてのAPIレスポンスは以下の標準構造に従います：

```json
{
  "data": {
    // 成功時のレスポンスデータ
  },
  "meta": {
    // ページネーション情報などのメタデータ
  },
  "message": "リクエスト処理のステータスメッセージ"
}
```

エラー発生時のレスポンス：

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "エラーの説明",
    "details": [
      // 詳細なエラー情報（オプション）
    ]
  }
}
```

### HTTPステータスコード

- **200 OK**: リクエスト成功
- **201 Created**: リソース作成成功
- **204 No Content**: 成功だがレスポンスボディなし
- **400 Bad Request**: リクエストが不正
- **401 Unauthorized**: 認証エラー
- **403 Forbidden**: 権限エラー
- **404 Not Found**: リソースが存在しない
- **422 Unprocessable Entity**: バリデーションエラー
- **500 Internal Server Error**: サーバーエラー

### ページネーション

リソースコレクションのエンドポイントはページネーションをサポートします：

- クエリパラメータ: `limit`（件数）と `offset`（開始位置）
- デフォルト値: `limit=20, offset=0`
- レスポンスの `meta` セクションにページネーション情報を含む

```json
"meta": {
  "total": 100,
  "limit": 20,
  "offset": 40,
  "next_offset": 60,
  "previous_offset": 20
}
```

### フィルタリングとソート

- フィルタリング: `filter[field_name]=value` 形式のクエリパラメータ
- 複雑なフィルタ: `filter={"created_at":{"gt":"2025-01-01"}}`
- ソート: `sort=field_name`（昇順）または `sort=-field_name`（降順）

### レート制限

- 時間あたりのリクエスト数に制限あり
- レスポンスヘッダで制限情報を提供
  - `X-RateLimit-Limit`: 制限数
  - `X-RateLimit-Remaining`: 残りリクエスト数
  - `X-RateLimit-Reset`: 制限リセット時間（Unix秒）

## バージョニング

- APIバージョンはURLパスに含まれる (`/v1/`)
- 重大な変更を含む更新時に新バージョンを公開
- 古いバージョンも一定期間サポート（移行期間を提供）

## エラーコード

主要なエラーコードと意味：

| コード | 説明 |
|-------|------|
| `AUTHENTICATION_ERROR` | 認証に失敗しました |
| `AUTHORIZATION_ERROR` | 権限がありません |
| `RESOURCE_NOT_FOUND` | リソースが見つかりません |
| `VALIDATION_ERROR` | 入力データが無効です |
| `RATE_LIMIT_EXCEEDED` | レート制限を超えました |
| `INTERNAL_ERROR` | 内部サーバーエラー |

## サンプルリクエスト

### cURLでのリクエスト例

```bash
curl -X GET "https://api.university-math-lms.example.com/v1/courses" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json"
```

### Pythonでのリクエスト例

```python
import requests

url = "https://api.university-math-lms.example.com/v1/courses"
headers = {
    "Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "Content-Type": "application/json"
}

response = requests.get(url, headers=headers)
data = response.json()
```

## データ型

APIで使用される主要なデータ型：

| データ型 | 形式 | 例 |
|---------|------|-----|
| UUID | RFC4122準拠の文字列 | `"550e8400-e29b-41d4-a716-446655440000"` |
| 日時 | ISO 8601形式 | `"2025-03-01T12:34:56Z"` |
| 真偽値 | JSONブール値 | `true`, `false` |
| 配列 | JSON配列 | `["項目1", "項目2"]` |
| オブジェクト | JSONオブジェクト | `{"key": "value"}` |

## セキュリティ考慮事項

- すべての通信はTLS 1.3以上を使用して暗号化
- 機密性の高いデータはAPIレスポンスで返さない
- トークンは適切な有効期限と更新メカニズムを実装
- バルク操作には回数制限を適用

## API健全性チェック

APIサーバーの状態確認用エンドポイント：

```
GET /health
```

レスポンス例：

```json
{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2025-03-11T02:30:00Z"
}
```
