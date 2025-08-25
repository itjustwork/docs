# 🔌 Тестирование API - Вопросы и Ответы

## Основы тестирования API

**Q: Что такое API тестирование и зачем оно нужно?**
A: API тестирование - это процесс проверки программных интерфейсов приложений (API) для обеспечения их корректной работы, производительности и безопасности.

**Преимущества API тестирования:**
- ✅ Раннее обнаружение ошибок
- ✅ Независимость от UI
- ✅ Быстрое выполнение тестов
- ✅ Высокая надежность
- ✅ Легкая автоматизация
- ✅ Тестирование бизнес-логики

**Q: Какие типы API тестирования существуют?**
A:

| Тип тестирования | Что проверяет | Примеры |
|------------------|---------------|---------|
| **Функциональное** | Корректность работы функций | CRUD операции, бизнес-логика |
| **Производительность** | Скорость и эффективность | Время отклика, пропускная способность |
| **Безопасность** | Защита от уязвимостей | Аутентификация, авторизация, валидация |
| **Нагрузочное** | Работа под нагрузкой | Множественные запросы, стресс-тестирование |
| **Интеграционное** | Взаимодействие компонентов | Связь между сервисами |
| **Контрактное** | Соответствие спецификации | OpenAPI/Swagger, схемы данных |

## Инструменты для тестирования API

**Q: Какие популярные инструменты для тестирования API?**
A:

### Postman
**Описание:** Графический интерфейс для тестирования API с возможностью автоматизации.

**Пример коллекции тестов:**
```javascript
// Pre-request Script для получения токена
pm.sendRequest({
    url: 'https://api.example.com/auth/login',
    method: 'POST',
    header: {
        'Content-Type': 'application/json'
    },
    body: {
        mode: 'raw',
        raw: JSON.stringify({
            email: 'user@example.com',
            password: 'password123'
        })
    }
}, function (err, response) {
    if (response.code === 200) {
        const token = response.json().token;
        pm.environment.set('auth_token', token);
    }
});

// Тест для проверки создания пользователя
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response time is less than 200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});

pm.test("User is created successfully", function () {
    const responseJson = pm.response.json();
    pm.expect(responseJson.id).to.be.a('number');
    pm.expect(responseJson.name).to.eql('John Doe');
    pm.expect(responseJson.email).to.eql('john@example.com');
});

pm.test("Response has required fields", function () {
    const responseJson = pm.response.json();
    pm.expect(responseJson).to.have.property('id');
    pm.expect(responseJson).to.have.property('name');
    pm.expect(responseJson).to.have.property('email');
    pm.expect(responseJson).to.have.property('created_at');
});
```

### REST Assured (Java)
**Описание:** Java библиотека для тестирования REST API.

**Пример тестов:**
```java
import io.restassured.RestAssured;
import io.restassured.http.ContentType;
import org.junit.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class UserAPITest {
    
    @Test
    public void testCreateUser() {
        RestAssured.baseURI = "https://api.example.com";
        
        given()
            .contentType(ContentType.JSON)
            .body("{\"name\":\"John Doe\",\"email\":\"john@example.com\"}")
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .body("id", notNullValue())
            .body("name", equalTo("John Doe"))
            .body("email", equalTo("john@example.com"))
            .time(lessThan(2000L));
    }
    
    @Test
    public void testGetUser() {
        given()
            .pathParam("id", 1)
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("name", notNullValue())
            .body("email", notNullValue());
    }
    
    @Test
    public void testUpdateUser() {
        given()
            .contentType(ContentType.JSON)
            .pathParam("id", 1)
            .body("{\"name\":\"Jane Doe\",\"email\":\"jane@example.com\"}")
        .when()
            .put("/users/{id}")
        .then()
            .statusCode(200)
            .body("name", equalTo("Jane Doe"))
            .body("email", equalTo("jane@example.com"));
    }
    
    @Test
    public void testDeleteUser() {
        given()
            .pathParam("id", 1)
        .when()
            .delete("/users/{id}")
        .then()
            .statusCode(204);
    }
}
```

### Newman (CLI для Postman)
**Описание:** Командная строка для запуска коллекций Postman.

**Пример использования:**
```bash
# Установка Newman
npm install -g newman

# Запуск коллекции
newman run collection.json

# Запуск с переменными окружения
newman run collection.json -e environment.json

# Запуск с отчетами
newman run collection.json --reporters cli,json,html

# Запуск в CI/CD
newman run collection.json --bail --reporter-cli-no-failures
```

## Тестирование REST API

**Q: Как тестировать REST API?**
A:

### Базовые HTTP методы
```python
import requests
import pytest

class TestUserAPI:
    base_url = "https://api.example.com"
    
    def test_get_users(self):
        """Тест получения списка пользователей"""
        response = requests.get(f"{self.base_url}/users")
        
        assert response.status_code == 200
        assert response.headers['Content-Type'] == 'application/json'
        
        users = response.json()
        assert isinstance(users, list)
        assert len(users) > 0
        
        # Проверка структуры данных
        for user in users:
            assert 'id' in user
            assert 'name' in user
            assert 'email' in user
    
    def test_create_user(self):
        """Тест создания пользователя"""
        user_data = {
            "name": "John Doe",
            "email": "john@example.com",
            "password": "password123"
        }
        
        response = requests.post(
            f"{self.base_url}/users",
            json=user_data,
            headers={'Content-Type': 'application/json'}
        )
        
        assert response.status_code == 201
        assert response.headers['Location'] is not None
        
        created_user = response.json()
        assert created_user['name'] == user_data['name']
        assert created_user['email'] == user_data['email']
        assert 'id' in created_user
        assert 'password' not in created_user  # Пароль не должен возвращаться
    
    def test_get_user_by_id(self):
        """Тест получения пользователя по ID"""
        user_id = 1
        response = requests.get(f"{self.base_url}/users/{user_id}")
        
        assert response.status_code == 200
        
        user = response.json()
        assert user['id'] == user_id
        assert 'name' in user
        assert 'email' in user
    
    def test_update_user(self):
        """Тест обновления пользователя"""
        user_id = 1
        update_data = {
            "name": "Jane Doe",
            "email": "jane@example.com"
        }
        
        response = requests.put(
            f"{self.base_url}/users/{user_id}",
            json=update_data,
            headers={'Content-Type': 'application/json'}
        )
        
        assert response.status_code == 200
        
        updated_user = response.json()
        assert updated_user['name'] == update_data['name']
        assert updated_user['email'] == update_data['email']
    
    def test_delete_user(self):
        """Тест удаления пользователя"""
        user_id = 1
        response = requests.delete(f"{self.base_url}/users/{user_id}")
        
        assert response.status_code == 204
        
        # Проверка, что пользователь действительно удален
        get_response = requests.get(f"{self.base_url}/users/{user_id}")
        assert get_response.status_code == 404
```

### Тестирование ошибок
```python
def test_invalid_user_id(self):
    """Тест запроса несуществующего пользователя"""
    response = requests.get(f"{self.base_url}/users/999999")
    
    assert response.status_code == 404
    assert response.json()['error'] == 'User not found'

def test_invalid_email_format(self):
    """Тест создания пользователя с неверным email"""
    user_data = {
        "name": "John Doe",
        "email": "invalid-email",
        "password": "password123"
    }
    
    response = requests.post(
        f"{self.base_url}/users",
        json=user_data,
        headers={'Content-Type': 'application/json'}
    )
    
    assert response.status_code == 400
    assert 'email' in response.json()['errors']

def test_missing_required_fields(self):
    """Тест создания пользователя без обязательных полей"""
    user_data = {
        "name": "John Doe"
        # Отсутствует email и password
    }
    
    response = requests.post(
        f"{self.base_url}/users",
        json=user_data,
        headers={'Content-Type': 'application/json'}
    )
    
    assert response.status_code == 400
    errors = response.json()['errors']
    assert 'email' in errors
    assert 'password' in errors
```

## Тестирование GraphQL API

**Q: Как тестировать GraphQL API?**
A:

### Базовые GraphQL запросы
```python
import requests
import json

class TestGraphQLAPI:
    base_url = "https://api.example.com/graphql"
    
    def test_query_users(self):
        """Тест GraphQL запроса пользователей"""
        query = """
        query {
            users {
                id
                name
                email
                posts {
                    id
                    title
                }
            }
        }
        """
        
        response = requests.post(
            self.base_url,
            json={'query': query},
            headers={'Content-Type': 'application/json'}
        )
        
        assert response.status_code == 200
        
        data = response.json()
        assert 'data' in data
        assert 'users' in data['data']
        
        users = data['data']['users']
        assert isinstance(users, list)
        
        for user in users:
            assert 'id' in user
            assert 'name' in user
            assert 'email' in user
            assert 'posts' in user
    
    def test_mutation_create_user(self):
        """Тест GraphQL мутации создания пользователя"""
        mutation = """
        mutation CreateUser($name: String!, $email: String!, $password: String!) {
            createUser(input: {name: $name, email: $email, password: $password}) {
                user {
                    id
                    name
                    email
                }
                errors {
                    field
                    message
                }
            }
        }
        """
        
        variables = {
            "name": "John Doe",
            "email": "john@example.com",
            "password": "password123"
        }
        
        response = requests.post(
            self.base_url,
            json={
                'query': mutation,
                'variables': variables
            },
            headers={'Content-Type': 'application/json'}
        )
        
        assert response.status_code == 200
        
        data = response.json()
        assert 'data' in data
        assert 'createUser' in data['data']
        
        result = data['data']['createUser']
        assert result['user'] is not None
        assert result['user']['name'] == variables['name']
        assert result['user']['email'] == variables['email']
        assert result['errors'] is None
```

## Тестирование аутентификации и авторизации

**Q: Как тестировать безопасность API?**
A:

### Тестирование JWT токенов
```python
import jwt
import time

class TestAPIAuthentication:
    base_url = "https://api.example.com"
    
    def test_login_success(self):
        """Тест успешного входа"""
        login_data = {
            "email": "user@example.com",
            "password": "password123"
        }
        
        response = requests.post(
            f"{self.base_url}/auth/login",
            json=login_data,
            headers={'Content-Type': 'application/json'}
        )
        
        assert response.status_code == 200
        
        data = response.json()
        assert 'token' in data
        assert 'refresh_token' in data
        
        # Проверка структуры JWT токена
        token = data['token']
        decoded = jwt.decode(token, options={"verify_signature": False})
        assert 'user_id' in decoded
        assert 'email' in decoded
        assert 'exp' in decoded
    
    def test_protected_endpoint_with_token(self):
        """Тест доступа к защищенному endpoint с токеном"""
        # Сначала получаем токен
        login_response = requests.post(
            f"{self.base_url}/auth/login",
            json={"email": "user@example.com", "password": "password123"}
        )
        token = login_response.json()['token']
        
        # Используем токен для доступа к защищенному ресурсу
        response = requests.get(
            f"{self.base_url}/users/profile",
            headers={'Authorization': f'Bearer {token}'}
        )
        
        assert response.status_code == 200
    
    def test_protected_endpoint_without_token(self):
        """Тест доступа к защищенному endpoint без токена"""
        response = requests.get(f"{self.base_url}/users/profile")
        
        assert response.status_code == 401
        assert response.json()['error'] == 'Unauthorized'
    
    def test_invalid_token(self):
        """Тест доступа с неверным токеном"""
        response = requests.get(
            f"{self.base_url}/users/profile",
            headers={'Authorization': 'Bearer invalid_token'}
        )
        
        assert response.status_code == 401
        assert response.json()['error'] == 'Invalid token'
    
    def test_expired_token(self):
        """Тест доступа с истекшим токеном"""
        # Создаем истекший токен
        expired_token = jwt.encode(
            {
                'user_id': 1,
                'email': 'user@example.com',
                'exp': time.time() - 3600  # Истек час назад
            },
            'secret',
            algorithm='HS256'
        )
        
        response = requests.get(
            f"{self.base_url}/users/profile",
            headers={'Authorization': f'Bearer {expired_token}'}
        )
        
        assert response.status_code == 401
        assert response.json()['error'] == 'Token expired'
```

## Контрактное тестирование

**Q: Что такое контрактное тестирование и как его использовать?**
A:

### Pact (Consumer-Driven Contracts)
**Описание:** Инструмент для контрактного тестирования между сервисами.

**Пример на Python:**
```python
from pact import Consumer, Provider
import requests
import pytest

# Создание контракта
pact = Consumer('UserService').has_pact_with(Provider('UserAPI'))

class TestUserAPIContract:
    
    def test_get_user_contract(self):
        """Тест контракта получения пользователя"""
        expected_user = {
            'id': 1,
            'name': 'John Doe',
            'email': 'john@example.com'
        }
        
        (pact
         .given('user exists')
         .upon_receiving('a request for user')
         .with_request('GET', '/users/1')
         .will_respond_with(200, body=expected_user))
        
        with pact:
            response = requests.get('http://localhost:1234/users/1')
            assert response.status_code == 200
            assert response.json() == expected_user
    
    def test_create_user_contract(self):
        """Тест контракта создания пользователя"""
        user_data = {
            'name': 'Jane Doe',
            'email': 'jane@example.com'
        }
        
        expected_response = {
            'id': 2,
            'name': 'Jane Doe',
            'email': 'jane@example.com',
            'created_at': '2023-01-01T00:00:00Z'
        }
        
        (pact
         .given('no user exists')
         .upon_receiving('a request to create user')
         .with_request('POST', '/users', body=user_data)
         .will_respond_with(201, body=expected_response))
        
        with pact:
            response = requests.post(
                'http://localhost:1234/users',
                json=user_data
            )
            assert response.status_code == 201
            result = response.json()
            assert result['name'] == expected_response['name']
            assert result['email'] == expected_response['email']
```

## Нагрузочное тестирование API

**Q: Как проводить нагрузочное тестирование API?**
A:

### Использование Locust
```python
from locust import HttpUser, task, between

class APIUser(HttpUser):
    wait_time = between(1, 3)
    
    def on_start(self):
        """Выполняется при запуске пользователя"""
        # Логин для получения токена
        response = self.client.post("/auth/login", json={
            "email": "user@example.com",
            "password": "password123"
        })
        self.token = response.json()['token']
    
    @task(3)
    def get_users(self):
        """Получение списка пользователей"""
        self.client.get("/users", headers={
            'Authorization': f'Bearer {self.token}'
        })
    
    @task(2)
    def get_user(self):
        """Получение конкретного пользователя"""
        user_id = 1
        self.client.get(f"/users/{user_id}", headers={
            'Authorization': f'Bearer {self.token}'
        })
    
    @task(1)
    def create_user(self):
        """Создание пользователя"""
        user_data = {
            "name": f"User {self.client.environment.runner.user_count}",
            "email": f"user{self.client.environment.runner.user_count}@example.com"
        }
        self.client.post("/users", json=user_data, headers={
            'Authorization': f'Bearer {self.token}'
        })
```

### Использование Artillery
```yaml
# artillery-config.yml
config:
  target: 'https://api.example.com'
  phases:
    - duration: 60
      arrivalRate: 10
    - duration: 120
      arrivalRate: 20
    - duration: 60
      arrivalRate: 5
  defaults:
    headers:
      Content-Type: 'application/json'

scenarios:
  - name: "API Load Test"
    weight: 1
    flow:
      - post:
          url: "/auth/login"
          json:
            email: "user@example.com"
            password: "password123"
          capture:
            - json: "$.token"
              as: "authToken"
      
      - get:
          url: "/users"
          headers:
            Authorization: "Bearer {{ authToken }}"
      
      - get:
          url: "/users/1"
          headers:
            Authorization: "Bearer {{ authToken }}"
      
      - post:
          url: "/users"
          json:
            name: "Test User"
            email: "test@example.com"
          headers:
            Authorization: "Bearer {{ authToken }}"
```

## Лучшие практики

**Q: Какие лучшие практики API тестирования?**
A:

### 1. Структура тестов
```python
import pytest
import requests
from typing import Dict, Any

class BaseAPITest:
    base_url: str = "https://api.example.com"
    auth_token: str = None
    
    def setup_method(self):
        """Настройка перед каждым тестом"""
        self.session = requests.Session()
        if self.auth_token:
            self.session.headers.update({
                'Authorization': f'Bearer {self.auth_token}'
            })
    
    def teardown_method(self):
        """Очистка после каждого теста"""
        self.session.close()
    
    def login(self, email: str, password: str) -> Dict[str, Any]:
        """Вспомогательный метод для входа"""
        response = self.session.post(
            f"{self.base_url}/auth/login",
            json={"email": email, "password": password}
        )
        return response.json()

class TestUserAPI(BaseAPITest):
    
    def test_user_crud_operations(self):
        """Полный CRUD тест для пользователей"""
        # Create
        user_data = {"name": "John", "email": "john@example.com"}
        create_response = self.session.post(
            f"{self.base_url}/users",
            json=user_data
        )
        assert create_response.status_code == 201
        user_id = create_response.json()['id']
        
        # Read
        get_response = self.session.get(f"{self.base_url}/users/{user_id}")
        assert get_response.status_code == 200
        assert get_response.json()['name'] == user_data['name']
        
        # Update
        update_data = {"name": "Jane", "email": "jane@example.com"}
        update_response = self.session.put(
            f"{self.base_url}/users/{user_id}",
            json=update_data
        )
        assert update_response.status_code == 200
        
        # Delete
        delete_response = self.session.delete(f"{self.base_url}/users/{user_id}")
        assert delete_response.status_code == 204
```

### 2. Валидация схем
```python
from jsonschema import validate

class TestAPISchema:
    
    def test_user_schema(self):
        """Тест соответствия схеме данных пользователя"""
        user_schema = {
            "type": "object",
            "properties": {
                "id": {"type": "integer"},
                "name": {"type": "string", "minLength": 1},
                "email": {"type": "string", "format": "email"},
                "created_at": {"type": "string", "format": "date-time"}
            },
            "required": ["id", "name", "email", "created_at"]
        }
        
        response = requests.get("https://api.example.com/users/1")
        user_data = response.json()
        
        # Валидация схемы
        validate(instance=user_data, schema=user_schema)
```

### 3. Тестирование производительности
```python
import time

class TestAPIPerformance:
    
    def test_response_time(self):
        """Тест времени отклика API"""
        start_time = time.time()
        response = requests.get("https://api.example.com/users")
        end_time = time.time()
        
        response_time = end_time - start_time
        assert response_time < 1.0  # Меньше 1 секунды
        assert response.status_code == 200
    
    def test_concurrent_requests(self):
        """Тест параллельных запросов"""
        import concurrent.futures
        
        def make_request():
            return requests.get("https://api.example.com/users")
        
        with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
            futures = [executor.submit(make_request) for _ in range(10)]
            responses = [future.result() for future in futures]
        
        # Все запросы должны быть успешными
        for response in responses:
            assert response.status_code == 200
```

## Полезные команды

**Q: Какие полезные команды для тестирования API?**
A:

```bash
# Тестирование с curl
curl -X GET https://api.example.com/users
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John","email":"john@example.com"}'

# Тестирование с httpie
http GET https://api.example.com/users
http POST https://api.example.com/users name=John email=john@example.com

# Запуск Newman
newman run collection.json -e environment.json --reporters cli,json,html

# Запуск Locust
locust -f locustfile.py --host=https://api.example.com

# Запуск Artillery
artillery run artillery-config.yml

# Тестирование с jq (для работы с JSON)
curl -s https://api.example.com/users | jq '.[0].name'
curl -s https://api.example.com/users | jq 'length'

# Мониторинг API с watch
watch -n 1 'curl -s https://api.example.com/health | jq'

# Тестирование с Apache Bench
ab -n 1000 -c 10 https://api.example.com/users

# Тестирование с wrk
wrk -t12 -c400 -d30s https://api.example.com/users
```
