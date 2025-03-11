# API設計原則

このドキュメントでは、大学生向け数学特化型学習管理システム（LMS）のAPI設計に関する原則と標準を定義します。

## API設計の基本方針

### RESTful API設計

- リソース中心の設計アプローチを採用
- HTTPメソッドを適切に使用（GET, POST, PUT, PATCH, DELETE）
- 一貫した命名規則とURLパスの構造
- ステートレスな通信

### API バージョニング

- URLパスによるバージョニング：`/api/v1/resources`
- 下位互換性の維持とバージョン移行計画

## エンドポイント構造

### 命名規則

- リソース名は複数形：`/api/v1/users`, `/api/v1/courses`
- リソース識別子はUUID：`/api/v1/users/{user_id}`
- 関連リソースはネスト表現：`/api/v1/courses/{course_id}/modules`
- アクションはHTTPメソッドで表現、特殊アクションのみパスに含める：`/api/v1/users/{user_id}/reset-password`

### 標準的なエンドポイントパターン

#### コレクションリソース
- GET `/api/v1/resources` - リソース一覧の取得
- POST `/api/v1/resources` - 新規リソースの作成

#### 単一リソース
- GET `/api/v1/resources/{id}` - 特定リソースの取得
- PUT `/api/v1/resources/{id}` - リソースの完全更新
- PATCH `/api/v1/resources/{id}` - リソースの部分更新
- DELETE `/api/v1/resources/{id}` - リソースの削除

## リクエスト・レスポンス形式

### リクエスト形式

- Content-Type: `application/json`
- リクエストボディは標準的なJSONフォーマット
- クエリパラメータはフィルタリング、ソート、ページネーションに使用

```json
// POSTリクエスト例
{
  "title": "微分積分学I",
  "description": "大学1年生向けの微積分コース",
  "is_published": false
}
```

### レスポンス形式

- 標準的なHTTPステータスコードを使用
- レスポンスボディは一貫した構造を持つJSON
- エラーレスポンスは詳細な情報を提供

```json
// 成功レスポンス例
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "title": "微分積分学I",
    "description": "大学1年生向けの微積分コース",
    "is_published": false,
    "created_at": "2025-03-01T12:00:00Z",
    "updated_at": "2025-03-01T12:00:00Z"
  },
  "message": "コースが正常に作成されました"
}

// エラーレスポンス例
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

## 認証・認可

### 認証方式

- JWTベースの認証
- `/api/v1/auth/login` でトークン取得
- Authorization ヘッダ（Bearer スキーム）でトークン送信

### 認可制御

- ロールベースのアクセス制御（RBAC）
- 各エンドポイントに必要な権限を定義
- 認可エラーは403 Forbiddenで返却

## ページネーションと結果制限

### ページネーション標準

- offset/limitベースのページネーション
- リクエスト: `?limit=10&offset=0`
- レスポンスにはメタデータを含む

```json
{
  "data": [...],
  "meta": {
    "total": 100,
    "limit": 10,
    "offset": 0,
    "next_offset": 10,
    "previous_offset": null
  }
}
```

### フィルタリングとソート

- クエリパラメータによるフィルタリング: `?status=active&role=student`
- 複雑なフィルタには専用パラメータ: `?filter={"created_at":{"gt":"2025-01-01"}}`
- ソートパラメータ: `?sort=name` (昇順) or `?sort=-name` (降順)

## レート制限とキャッシング

### レート制限

- APIリクエスト数の制限を実装
- レスポンスヘッダで制限情報を提供
  - X-RateLimit-Limit
  - X-RateLimit-Remaining
  - X-RateLimit-Reset

### キャッシング

- ETagヘッダによるキャッシュ検証
- Cache-Controlヘッダでキャッシュ動作を制御
- GET要求のみキャッシュ可能

## API ドキュメント

### OpenAPI仕様

- OpenAPI 3.0形式でAPIを文書化
- SwaggerUIによるインタラクティブドキュメント提供
- 各エンドポイントの詳細な説明、パラメータ、レスポンス例を含む

### APIエンドポイントの自己文書化

- HATEOAS原則に従い、関連リソースへのリンクを含める
- OPTIONS要求でエンドポイントのメタデータを提供

## セキュリティ考慮事項

### 入力検証

- すべてのリクエストパラメータとボディを厳格に検証
- SQLインジェクション、XSS攻撃の防止
- バリデーションエラーは明確なメッセージで返却

### センシティブデータの保護

- 個人情報などのセンシティブデータは暗号化
- レスポンスからセンシティブデータを除外
- TLS 1.3による通信の暗号化
