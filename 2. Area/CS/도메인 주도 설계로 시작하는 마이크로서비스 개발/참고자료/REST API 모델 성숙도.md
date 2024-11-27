### 점진적인 차이 요약
1. **레벨 0**: 단일 엔드포인트로 모든 요청을 처리하며, 동작은 본문에서 정의됩니다.
2. **레벨 1**: 리소스 중심의 URI 설계를 통해 엔드포인트를 구분합니다.
3. **레벨 2**: HTTP 메서드를 사용하여 CRUD 작업을 명확히 구분합니다.
4. **레벨 3**: 하이퍼미디어 링크를 사용하여 클라이언트가 동적으로 리소스와 상호작용할 수 있게 합니다.
### 레벨 0: 단일 엔드포인트 (The Swamp of POX)

- **설명**: 모든 요청을 단일 엔드포인트로 처리합니다. 주로 XML이나 JSON 형식의 메시지 본문을 사용합니다.
- **특징**
    - 단일 URL로 모든 요청을 처리.
    - 주로 POST 메서드 사용.
    - 동작 구분은 본문에서 이루어짐.
        
        ```
        http코드 복사
        POST /api
        Content-Type: application/json
        
        {
          "action": "getUser",
          "userId": 123
        }
        
        ```
        
### 레벨 1: 리소스 (Resources)
- **설명**: 리소스를 개별 엔드포인트로 구분합니다.
- **특징**:
    - 각 리소스에 대해 고유한 URL.
    - 리소스 중심의 URI 설계.
    - 주로 GET 메서드를 사용, 다른 HTTP 메서드는 제한적으로 사용.
        
        ```java
        GET /users
        GET /users/123
        GET /orders
        GET /orders/456
        
        ```

### 레벨 2: HTTP 메서드 (HTTP Verbs)
- **설명**: HTTP 메서드를 사용하여 리소스에 대한 다양한 작업을 표현합니다.
- **특징**:
    - HTTP 메서드(GET, POST, PUT, DELETE 등)를 사용하여 작업 구분  
    - CRUD 작업을 명확히 구분.
    - 클라이언트가 URI와 메서드에 따라 동작을 수행.
        
        ```java
        GET /users          # 사용자 목록 조회
        POST /users         # 새 사용자 생성
        GET /users/123      # 특정 사용자 조회
        PUT /users/123      # 특정 사용자 업데이트
        DELETE /users/123   # 특정 사용자 삭제
        
        ```

### 레벨 3: HATEOAS (Hypermedia As The Engine Of Application State)
- **설명**: 하이퍼미디어 링크를 사용하여 리소스 간의 관계와 가능한 작업을 표현합니다.
- 주요 개념
    1. **자기 설명적 응답**: 응답 자체가 클라이언트가 할 수 있는 모든 가능한 동작을 설명해야 합니다. 즉, 클라이언트는 응답을 통해 다음에 무엇을 할 수 있는지 알 수 있습니다.
    2. **동적 상호작용**: 클라이언트는 서버로부터 받은 하이퍼미디어 링크를 사용하여 동적으로 리소스와 상호작용합니다. 이는 API의 버전 변경이나 확장이 있어도 클라이언트가 하드코딩된 URI나 동작을 사용하지 않게 해줍니다.
    3. **상태 기반 전이**: 응답 내의 링크를 따라가며 상태를 전이합니다. 클라이언트는 특정 리소스의 상태를 조회하고, 그 상태에 따라 다음에 수행할 수 있는 작업을 링크를 통해 확인합니다.
### HATEOAS 구현 예시
HATEOAS의 개념을 구현한 예시를 통해 자세히 설명해보겠습니다. 예를 들어, 사용자 정보와 관련된 API를 가정해보겠습니다.

### 1. 사용자 목록 조회 (GET /users)

사용자 목록을 조회하는 요청을 보냈을 때, 서버는 각 사용자에 대한 링크를 포함하여 응답을 반환합니다.

```
http코드 복사
GET /users

HTTP/1.1 200 OK
Content-Type: application/json

{
  "users": [
    {
      "id": 123,
      "name": "John Doe",
      "links": {
        "self": "/users/123",
        "update": "/users/123",
        "delete": "/users/123"
      }
    },
    {
      "id": 456,
      "name": "Jane Doe",
      "links": {
        "self": "/users/456",
        "update": "/users/456",
        "delete": "/users/456"
      }
    }
  ],
  "links": {
    "create": "/users"
  }
}

```

### 2. 특정 사용자 조회 (GET /users/123)
특정 사용자의 상세 정보를 조회할 때, 응답에는 해당 사용자의 정보를 수정하거나 삭제할 수 있는 링크가 포함됩니다.

```
http코드 복사
GET /users/123

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "links": {
    "self": "/users/123",
    "update": "/users/123",
    "delete": "/users/123",
    "orders": "/users/123/orders"
  }
}

```

### 3. 사용자 정보 업데이트 (PUT /users/123)
사용자 정보를 업데이트하는 요청을 보낸 후, 서버는 성공적으로 업데이트된 사용자 정보를 반환하고, 관련 링크를 포함시킵니다.

```
http코드 복사
PUT /users/123
Content-Type: application/json

{
  "name": "John Doe Updated",
  "email": "john.updated@example.com"
}

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe Updated",
  "email": "john.updated@example.com",
  "links": {
    "self": "/users/123",
    "update": "/users/123",
    "delete": "/users/123",
    "orders": "/users/123/orders"
  }
}

```

### HATEOAS의 장점
1. **확장성**: API의 구조나 엔드포인트가 변경되더라도 클라이언트는 링크를 따라가며 필요한 작업을 수행할 수 있습니다.
2. **유연성**: 클라이언트는 응답에 포함된 링크를 통해 현재 리소스와 연관된 모든 가능한 작업을 동적으로 탐색할 수 있습니다.
3. **유지보수 용이성**: 클라이언트와 서버 간의 결합도가 낮아지므로, 서버 측의 변경이 클라이언트에 큰 영향을 미치지 않습니다.