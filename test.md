```mermaid
sequenceDiagram
    participant C as Client
    participant API as API Gateway REST API
    participant CA as Cognito Authorizer
    participant L as Lambda Function
    participant DB as Database

    C->>API: 1. GETリクエスト
    API->>CA: 2. Authorization Token検証
    alt 認証NG
        CA->>API: 2.1 認証NG
        API->>C: 401 Unauthorized
    else 認可NG
        CA->>API: 2.2 認可NG
        API->>C: 403 Forbidden
    else 認証/認可OK
        CA->>API: 2.3 認証/認可OK
        API->>L: 3. Lambda Functionにリクエスト
        L->>DB: 4. データ取得
        alt データが見つからない
            DB->>L: 4.1 データが見つからない
            L->>API: 4.2 取得NG
            API->>C: 404 Not Found
        else データが見つかる
            DB->>L: 4.3 データが見つかる
            L->>API: 5. 取得OK
            API->>C: 6. 200 OK
        end
    end
    ```
