# 🔒 Тестирование безопасности - Вопросы и Ответы

## Основы тестирования безопасности

**Q: Что такое тестирование безопасности и зачем оно нужно?**
A: Тестирование безопасности - это процесс выявления уязвимостей и слабых мест в приложениях, которые могут быть использованы злоумышленниками для несанкционированного доступа к данным или системам.

**Цели тестирования безопасности:**
- ✅ Выявление уязвимостей и слабых мест
- ✅ Проверка соответствия стандартам безопасности
- ✅ Оценка рисков безопасности
- ✅ Защита конфиденциальных данных
- ✅ Соответствие требованиям регуляторов
- ✅ Предотвращение атак и инцидентов

**Q: Какие типы тестирования безопасности существуют?**
A:

| Тип тестирования | Описание | Примеры |
|------------------|----------|---------|
| **Статическое тестирование (SAST)** | Анализ исходного кода без выполнения | SonarQube, Checkmarx, Fortify |
| **Динамическое тестирование (DAST)** | Тестирование работающего приложения | OWASP ZAP, Burp Suite, Acunetix |
| **Интерактивное тестирование (IAST)** | Анализ во время выполнения приложения | Contrast Security, Hdiv |
| **Тестирование на проникновение** | Имитация реальных атак | Metasploit, Nmap, Kali Linux |
| **Аудит безопасности** | Проверка политик и процедур | Ручная проверка, интервью |
| **Тестирование конфигурации** | Проверка настроек безопасности | CIS Benchmarks, NIST |

## OWASP Top 10

**Q: Что такое OWASP Top 10 и как тестировать эти уязвимости?**
A: OWASP Top 10 - это список наиболее критичных уязвимостей веб-приложений, составленный Open Web Application Security Project.

### 1. Broken Access Control (A01:2021)
**Описание:** Неправильная реализация контроля доступа.

**Пример тестирования:**
```python
import requests
import pytest

class TestAccessControl:
    base_url = "https://api.example.com"
    
    def test_horizontal_privilege_escalation(self):
        """Тест горизонтального повышения привилегий"""
        # Логин как пользователь 1
        user1_token = self.login("user1@example.com", "password123")
        
        # Попытка доступа к данным пользователя 2
        response = requests.get(
            f"{self.base_url}/users/2/profile",
            headers={'Authorization': f'Bearer {user1_token}'}
        )
        
        # Должно быть 403 Forbidden
        assert response.status_code == 403
    
    def test_vertical_privilege_escalation(self):
        """Тест вертикального повышения привилегий"""
        # Логин как обычный пользователь
        user_token = self.login("user@example.com", "password123")
        
        # Попытка доступа к админским функциям
        response = requests.post(
            f"{self.base_url}/admin/users",
            headers={'Authorization': f'Bearer {user_token}'},
            json={"action": "delete", "user_id": 1}
        )
        
        # Должно быть 403 Forbidden
        assert response.status_code == 403
    
    def test_missing_authentication(self):
        """Тест отсутствия аутентификации"""
        # Попытка доступа к защищенному ресурсу без токена
        response = requests.get(f"{self.base_url}/admin/users")
        
        # Должно быть 401 Unauthorized
        assert response.status_code == 401
    
    def login(self, email, password):
        """Вспомогательный метод для входа"""
        response = requests.post(
            f"{self.base_url}/auth/login",
            json={"email": email, "password": password}
        )
        return response.json()['token']
```

### 2. Cryptographic Failures (A02:2021)
**Описание:** Неправильное использование криптографии.

**Пример тестирования:**
```python
import requests
import ssl
import socket
from cryptography import x509
from cryptography.hazmat.backends import default_backend

class TestCryptographicFailures:
    
    def test_weak_ssl_tls(self):
        """Тест слабых SSL/TLS настроек"""
        hostname = "example.com"
        port = 443
        
        # Проверка поддерживаемых протоколов
        context = ssl.create_default_context()
        with socket.create_connection((hostname, port)) as sock:
            with context.wrap_socket(sock, server_hostname=hostname) as ssock:
                version = ssock.version()
                cipher = ssock.cipher()
                
                # Проверка версии TLS
                assert "TLSv1.2" in version or "TLSv1.3" in version
                
                # Проверка шифров
                weak_ciphers = ['RC4', 'DES', '3DES', 'MD5']
                for weak_cipher in weak_ciphers:
                    assert weak_cipher not in cipher[0]
    
    def test_weak_password_hashing(self):
        """Тест слабого хеширования паролей"""
        # Проверка, что пароли не передаются в открытом виде
        response = requests.post(
            "https://api.example.com/auth/login",
            json={"email": "user@example.com", "password": "password123"}
        )
        
        # Проверка, что в ответе нет хеша пароля
        response_data = response.json()
        assert 'password' not in response_data
        assert 'password_hash' not in response_data
    
    def test_sensitive_data_exposure(self):
        """Тест утечки чувствительных данных"""
        # Проверка, что чувствительные данные не передаются в ответах
        response = requests.get(
            "https://api.example.com/users/1",
            headers={'Authorization': 'Bearer valid_token'}
        )
        
        user_data = response.json()
        sensitive_fields = ['password', 'ssn', 'credit_card', 'api_key']
        
        for field in sensitive_fields:
            assert field not in user_data
```

### 3. Injection (A03:2021)
**Описание:** Внедрение вредоносного кода.

**Пример тестирования SQL Injection:**
```python
import requests
import time

class TestSQLInjection:
    base_url = "https://api.example.com"
    
    def test_sql_injection_login(self):
        """Тест SQL injection в форме логина"""
        payloads = [
            "' OR '1'='1",
            "' OR 1=1--",
            "admin'--",
            "' UNION SELECT 1,2,3--",
            "'; DROP TABLE users--"
        ]
        
        for payload in payloads:
            response = requests.post(
                f"{self.base_url}/auth/login",
                json={"email": payload, "password": "password123"}
            )
            
            # Проверка, что не удалось войти с SQL injection
            assert response.status_code != 200 or "token" not in response.json()
    
    def test_sql_injection_search(self):
        """Тест SQL injection в поиске"""
        payloads = [
            "' OR '1'='1",
            "'; SELECT * FROM users--",
            "' UNION SELECT username,password FROM users--"
        ]
        
        for payload in payloads:
            response = requests.get(
                f"{self.base_url}/users/search",
                params={"q": payload}
            )
            
            # Проверка, что не произошла утечка данных
            assert response.status_code != 200 or len(response.json()) == 0
    
    def test_blind_sql_injection(self):
        """Тест слепой SQL injection"""
        # Тест с временной задержкой
        start_time = time.time()
        response = requests.get(
            f"{self.base_url}/users/1",
            params={"id": "1' AND (SELECT SLEEP(5))--"}
        )
        end_time = time.time()
        
        # Если есть уязвимость, запрос должен занять 5+ секунд
        if end_time - start_time > 5:
            print("WARNING: Possible blind SQL injection detected!")
```

**Пример тестирования XSS:**
```python
class TestXSS:
    
    def test_reflected_xss(self):
        """Тест отраженного XSS"""
        payloads = [
            "<script>alert('XSS')</script>",
            "javascript:alert('XSS')",
            "<img src=x onerror=alert('XSS')>",
            "<svg onload=alert('XSS')>",
            "';alert('XSS');//"
        ]
        
        for payload in payloads:
            response = requests.get(
                "https://api.example.com/search",
                params={"q": payload}
            )
            
            # Проверка, что payload не отражен в ответе
            assert payload not in response.text
    
    def test_stored_xss(self):
        """Тест сохраненного XSS"""
        payload = "<script>alert('XSS')</script>"
        
        # Отправка payload
        response = requests.post(
            "https://api.example.com/comments",
            json={"content": payload},
            headers={'Authorization': 'Bearer valid_token'}
        )
        
        # Получение комментария
        comment_id = response.json()['id']
        get_response = requests.get(f"https://api.example.com/comments/{comment_id}")
        
        # Проверка, что payload не сохранен
        assert payload not in get_response.text
```

### 4. Insecure Design (A04:2021)
**Описание:** Небезопасная архитектура и дизайн.

**Пример тестирования:**
```python
class TestInsecureDesign:
    
    def test_predictable_resource_ids(self):
        """Тест предсказуемых ID ресурсов"""
        # Проверка, что ID не являются последовательными
        ids = []
        for i in range(10):
            response = requests.get(f"https://api.example.com/users/{i+1}")
            if response.status_code == 200:
                user_id = response.json()['id']
                ids.append(user_id)
        
        # Проверка, что ID не являются простыми числами
        for user_id in ids:
            assert user_id > 1000  # Должны быть достаточно большими
    
    def test_mass_assignment(self):
        """Тест массового присваивания"""
        # Попытка установить привилегии через обычные поля
        user_data = {
            "name": "Test User",
            "email": "test@example.com",
            "role": "admin",  # Не должно быть разрешено
            "is_active": True,
            "permissions": ["read", "write", "delete"]
        }
        
        response = requests.post(
            "https://api.example.com/users",
            json=user_data
        )
        
        if response.status_code == 201:
            created_user = response.json()
            # Проверка, что привилегии не были установлены
            assert created_user.get('role') != 'admin'
            assert 'permissions' not in created_user
```

## Инструменты тестирования безопасности

**Q: Какие инструменты используются для тестирования безопасности?**
A:

### OWASP ZAP
**Описание:** Бесплатный инструмент для автоматического тестирования безопасности.

**Пример использования:**
```python
from zapv2 import ZAPv2
import time

class ZAPSecurityTest:
    
    def __init__(self):
        self.zap = ZAPv2(proxies={'http': 'http://localhost:8080', 'https': 'http://localhost:8080'})
    
    def run_security_scan(self, target_url):
        """Запуск полного сканирования безопасности"""
        print(f"Начинаем сканирование {target_url}")
        
        # Пассивное сканирование
        print("Запуск пассивного сканирования...")
        self.zap.urlopen(target_url)
        time.sleep(2)
        
        # Активное сканирование
        print("Запуск активного сканирования...")
        scan_id = self.zap.ascan.scan(target_url)
        
        # Ожидание завершения сканирования
        while int(self.zap.ascan.status(scan_id)) < 100:
            print(f"Прогресс сканирования: {self.zap.ascan.status(scan_id)}%")
            time.sleep(5)
        
        # Получение результатов
        alerts = self.zap.core.alerts()
        
        # Анализ результатов
        high_alerts = [alert for alert in alerts if alert['risk'] == 'High']
        medium_alerts = [alert for alert in alerts if alert['risk'] == 'Medium']
        
        print(f"Найдено высоких рисков: {len(high_alerts)}")
        print(f"Найдено средних рисков: {len(medium_alerts)}")
        
        return {
            'high_alerts': high_alerts,
            'medium_alerts': medium_alerts,
            'all_alerts': alerts
        }
    
    def test_specific_vulnerability(self, target_url, vulnerability_type):
        """Тест конкретной уязвимости"""
        if vulnerability_type == 'sql_injection':
            # Тест SQL injection
            test_urls = [
                f"{target_url}/search?q=test'",
                f"{target_url}/user?id=1'",
                f"{target_url}/product?id=1' OR '1'='1"
            ]
            
            for url in test_urls:
                self.zap.urlopen(url)
                time.sleep(1)
        
        elif vulnerability_type == 'xss':
            # Тест XSS
            payloads = [
                "<script>alert('XSS')</script>",
                "javascript:alert('XSS')",
                "<img src=x onerror=alert('XSS')>"
            ]
            
            for payload in payloads:
                test_url = f"{target_url}/search?q={payload}"
                self.zap.urlopen(test_url)
                time.sleep(1)
        
        # Получение результатов
        alerts = self.zap.core.alerts()
        return [alert for alert in alerts if vulnerability_type.lower() in alert['name'].lower()]

# Использование
zap_test = ZAPSecurityTest()
results = zap_test.run_security_scan("https://example.com")
```

### Burp Suite
**Описание:** Профессиональный инструмент для тестирования безопасности веб-приложений.

**Пример автоматизации:**
```python
import requests
from burp import IBurpExtender
from burp import IScannerCheck
from burp import IScanIssue

class CustomSecurityScanner(IBurpExtender, IScannerCheck):
    
    def registerExtenderCallbacks(self, callbacks):
        self._callbacks = callbacks
        self._helpers = callbacks.getHelpers()
        callbacks.setExtensionName("Custom Security Scanner")
        callbacks.registerScannerCheck(self)
    
    def doPassiveScan(self, baseRequestResponse):
        """Пассивное сканирование"""
        issues = []
        
        # Проверка заголовков безопасности
        response = baseRequestResponse.getResponse()
        response_info = self._helpers.analyzeResponse(response)
        headers = response_info.getHeaders()
        
        # Проверка отсутствия заголовков безопасности
        security_headers = [
            'X-Content-Type-Options',
            'X-Frame-Options',
            'X-XSS-Protection',
            'Strict-Transport-Security'
        ]
        
        for header in security_headers:
            if not any(header in h for h in headers):
                issues.append(self.create_issue(
                    baseRequestResponse,
                    f"Missing Security Header: {header}",
                    "The application is missing important security headers."
                ))
        
        return issues
    
    def doActiveScan(self, baseRequestResponse, insertionPoint):
        """Активное сканирование"""
        issues = []
        
        # Тест SQL injection
        sql_payloads = [
            "' OR '1'='1",
            "'; DROP TABLE users--",
            "' UNION SELECT 1,2,3--"
        ]
        
        for payload in sql_payloads:
            # Создание запроса с payload
            checkRequest = insertionPoint.buildRequest(payload)
            checkRequestResponse = self._callbacks.makeHttpRequest(
                baseRequestResponse.getHttpService(), checkRequest
            )
            
            # Анализ ответа на признаки SQL injection
            if self.detect_sql_injection(checkRequestResponse):
                issues.append(self.create_issue(
                    checkRequestResponse,
                    "SQL Injection Vulnerability",
                    f"SQL injection detected with payload: {payload}"
                ))
        
        return issues
    
    def detect_sql_injection(self, response):
        """Обнаружение SQL injection по ответу"""
        response_data = response.getResponse()
        response_string = self._helpers.bytesToString(response_data)
        
        # Признаки SQL injection
        sql_errors = [
            "sql syntax",
            "mysql_fetch_array",
            "ora-",
            "sql server",
            "postgresql",
            "sqlite"
        ]
        
        return any(error.lower() in response_string.lower() for error in sql_errors)
    
    def create_issue(self, requestResponse, name, description):
        """Создание отчета об уязвимости"""
        return CustomScanIssue(
            requestResponse.getHttpService(),
            self._helpers.analyzeRequest(requestResponse).getUrl(),
            [requestResponse],
            name,
            description,
            "High",
            "Certain"
        )

class CustomScanIssue(IScanIssue):
    def __init__(self, httpService, url, httpMessages, name, description, severity, confidence):
        self._httpService = httpService
        self._url = url
        self._httpMessages = httpMessages
        self._name = name
        self._description = description
        self._severity = severity
        self._confidence = confidence
    
    def getUrl(self):
        return self._url
    
    def getIssueName(self):
        return self._name
    
    def getIssueType(self):
        return 0
    
    def getSeverity(self):
        return self._severity
    
    def getConfidence(self):
        return self._confidence
    
    def getIssueBackground(self):
        return self._description
    
    def getRemediationBackground(self):
        return "Implement proper input validation and use parameterized queries."
    
    def getIssueDetail(self):
        return self._description
    
    def getRemediationDetail(self):
        return "Use prepared statements and input validation."
    
    def getHttpMessages(self):
        return self._httpMessages
    
    def getHttpService(self):
        return self._httpService
```

## Автоматизированное тестирование безопасности

**Q: Как автоматизировать тестирование безопасности?**
A:

### Интеграция в CI/CD
```yaml
# .github/workflows/security-test.yml
name: Security Testing

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'
    
    - name: Install dependencies
      run: |
        pip install bandit safety
        npm install -g audit
    
    - name: Run Bandit (Python security scanner)
      run: |
        bandit -r . -f json -o bandit-report.json
        bandit -r . -f txt -o bandit-report.txt
    
    - name: Run Safety (Python dependency scanner)
      run: |
        safety check --json --output safety-report.json
        safety check --output safety-report.txt
    
    - name: Run npm audit
      run: |
        npm audit --audit-level=moderate --json > npm-audit-report.json
        npm audit --audit-level=moderate > npm-audit-report.txt
    
    - name: Upload security reports
      uses: actions/upload-artifact@v2
      with:
        name: security-reports
        path: |
          bandit-report.json
          bandit-report.txt
          safety-report.json
          safety-report.txt
          npm-audit-report.json
          npm-audit-report.txt
    
    - name: Comment PR with security findings
      if: github.event_name == 'pull_request'
      uses: actions/github-script@v6
      with:
        script: |
          const fs = require('fs');
          
          let comment = '## 🔒 Security Scan Results\n\n';
          
          // Read Bandit results
          if (fs.existsSync('bandit-report.txt')) {
            const banditResults = fs.readFileSync('bandit-report.txt', 'utf8');
            comment += '### Python Security Issues (Bandit)\n';
            comment += '```\n' + banditResults + '\n```\n\n';
          }
          
          // Read Safety results
          if (fs.existsSync('safety-report.txt')) {
            const safetyResults = fs.readFileSync('safety-report.txt', 'utf8');
            comment += '### Python Dependencies (Safety)\n';
            comment += '```\n' + safetyResults + '\n```\n\n';
          }
          
          // Read npm audit results
          if (fs.existsSync('npm-audit-report.txt')) {
            const npmResults = fs.readFileSync('npm-audit-report.txt', 'utf8');
            comment += '### Node.js Dependencies (npm audit)\n';
            comment += '```\n' + npmResults + '\n```\n\n';
          }
          
          github.rest.issues.createComment({
            issue_number: context.issue.number,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: comment
          });
```

### Автоматизированные тесты безопасности
```python
import pytest
import requests
import subprocess
import json
from typing import List, Dict

class SecurityTestSuite:
    
    def __init__(self, base_url: str):
        self.base_url = base_url
        self.session = requests.Session()
    
    def test_authentication_bypass(self):
        """Тест обхода аутентификации"""
        # Попытка доступа к защищенным ресурсам без аутентификации
        protected_endpoints = [
            "/admin/users",
            "/api/sensitive-data",
            "/user/profile",
            "/admin/settings"
        ]
        
        for endpoint in protected_endpoints:
            response = self.session.get(f"{self.base_url}{endpoint}")
            assert response.status_code in [401, 403], f"Endpoint {endpoint} accessible without authentication"
    
    def test_sql_injection_scan(self):
        """Сканирование на SQL injection"""
        payloads = [
            "' OR '1'='1",
            "'; DROP TABLE users--",
            "' UNION SELECT 1,2,3--",
            "admin'--",
            "1' AND (SELECT COUNT(*) FROM users) > 0--"
        ]
        
        vulnerable_endpoints = []
        
        # Тестируем различные endpoints
        test_endpoints = [
            "/search?q={payload}",
            "/user?id={payload}",
            "/product?id={payload}",
            "/api/users?filter={payload}"
        ]
        
        for endpoint_template in test_endpoints:
            for payload in payloads:
                endpoint = endpoint_template.format(payload=payload)
                response = self.session.get(f"{self.base_url}{endpoint}")
                
                # Проверяем признаки SQL injection
                if self.detect_sql_injection_response(response):
                    vulnerable_endpoints.append(endpoint)
        
        assert len(vulnerable_endpoints) == 0, f"SQL injection vulnerabilities found: {vulnerable_endpoints}"
    
    def test_xss_scan(self):
        """Сканирование на XSS"""
        payloads = [
            "<script>alert('XSS')</script>",
            "javascript:alert('XSS')",
            "<img src=x onerror=alert('XSS')>",
            "<svg onload=alert('XSS')>",
            "';alert('XSS');//"
        ]
        
        vulnerable_endpoints = []
        
        # Тестируем формы и параметры
        test_data = [
            {"endpoint": "/search", "param": "q"},
            {"endpoint": "/comment", "param": "content"},
            {"endpoint": "/user/profile", "param": "name"}
        ]
        
        for test in test_data:
            for payload in payloads:
                data = {test["param"]: payload}
                response = self.session.post(f"{self.base_url}{test['endpoint']}", data=data)
                
                # Проверяем, что payload не отражен в ответе
                if payload in response.text:
                    vulnerable_endpoints.append(f"{test['endpoint']} ({test['param']})")
        
        assert len(vulnerable_endpoints) == 0, f"XSS vulnerabilities found: {vulnerable_endpoints}"
    
    def test_csrf_protection(self):
        """Тест защиты от CSRF"""
        # Проверяем наличие CSRF токенов в формах
        response = self.session.get(f"{self.base_url}/login")
        
        # Ищем CSRF токены
        csrf_tokens = [
            'csrf_token',
            '_token',
            'authenticity_token',
            'csrf'
        ]
        
        has_csrf_protection = any(token in response.text for token in csrf_tokens)
        assert has_csrf_protection, "No CSRF protection detected"
    
    def test_security_headers(self):
        """Тест заголовков безопасности"""
        response = self.session.get(self.base_url)
        headers = response.headers
        
        # Проверяем наличие важных заголовков безопасности
        security_headers = {
            'X-Content-Type-Options': 'nosniff',
            'X-Frame-Options': ['DENY', 'SAMEORIGIN'],
            'X-XSS-Protection': '1; mode=block',
            'Strict-Transport-Security': None,  # Любое значение
            'Content-Security-Policy': None     # Любое значение
        }
        
        missing_headers = []
        for header, expected_value in security_headers.items():
            if header not in headers:
                missing_headers.append(header)
            elif expected_value and headers[header] not in expected_value:
                missing_headers.append(f"{header} (incorrect value)")
        
        assert len(missing_headers) == 0, f"Missing security headers: {missing_headers}"
    
    def test_sensitive_data_exposure(self):
        """Тест утечки чувствительных данных"""
        # Проверяем, что чувствительные данные не передаются
        response = self.session.get(f"{self.base_url}/api/users/1")
        
        if response.status_code == 200:
            user_data = response.json()
            sensitive_fields = ['password', 'ssn', 'credit_card', 'api_key', 'secret']
            
            exposed_fields = [field for field in sensitive_fields if field in user_data]
            assert len(exposed_fields) == 0, f"Sensitive data exposed: {exposed_fields}"
    
    def detect_sql_injection_response(self, response) -> bool:
        """Обнаружение SQL injection по ответу"""
        sql_errors = [
            'sql syntax',
            'mysql_fetch_array',
            'ora-',
            'sql server',
            'postgresql',
            'sqlite',
            'mysql_num_rows',
            'mysql_fetch_assoc'
        ]
        
        response_text = response.text.lower()
        return any(error in response_text for error in sql_errors)

# Использование в pytest
@pytest.fixture
def security_tester():
    return SecurityTestSuite("https://example.com")

def test_security_suite(security_tester):
    """Полный набор тестов безопасности"""
    security_tester.test_authentication_bypass()
    security_tester.test_sql_injection_scan()
    security_tester.test_xss_scan()
    security_tester.test_csrf_protection()
    security_tester.test_security_headers()
    security_tester.test_sensitive_data_exposure()
```

## Лучшие практики

**Q: Какие лучшие практики тестирования безопасности?**
A:

### 1. Планирование тестирования безопасности
```python
class SecurityTestPlan:
    def __init__(self):
        self.test_categories = []
        self.risk_levels = ['Low', 'Medium', 'High', 'Critical']
    
    def add_test_category(self, category, description, risk_level):
        """Добавление категории тестов"""
        self.test_categories.append({
            'category': category,
            'description': description,
            'risk_level': risk_level,
            'tests': []
        })
    
    def add_test(self, category, test_name, test_function, expected_result):
        """Добавление теста в категорию"""
        for cat in self.test_categories:
            if cat['category'] == category:
                cat['tests'].append({
                    'name': test_name,
                    'function': test_function,
                    'expected': expected_result
                })
                break
    
    def generate_report(self):
        """Генерация отчета о тестировании"""
        report = {
            'summary': {
                'total_tests': 0,
                'passed': 0,
                'failed': 0,
                'critical_issues': 0
            },
            'categories': []
        }
        
        for category in self.test_categories:
            cat_report = {
                'name': category['category'],
                'risk_level': category['risk_level'],
                'tests': []
            }
            
            for test in category['tests']:
                try:
                    result = test['function']()
                    passed = result == test['expected']
                    
                    cat_report['tests'].append({
                        'name': test['name'],
                        'passed': passed,
                        'result': result
                    })
                    
                    report['summary']['total_tests'] += 1
                    if passed:
                        report['summary']['passed'] += 1
                    else:
                        report['summary']['failed'] += 1
                        if category['risk_level'] == 'Critical':
                            report['summary']['critical_issues'] += 1
                            
                except Exception as e:
                    cat_report['tests'].append({
                        'name': test['name'],
                        'passed': False,
                        'error': str(e)
                    })
                    report['summary']['total_tests'] += 1
                    report['summary']['failed'] += 1
            
            report['categories'].append(cat_report)
        
        return report

# Использование
plan = SecurityTestPlan()
plan.add_test_category('Authentication', 'Tests for authentication bypass', 'Critical')
plan.add_test_category('Input Validation', 'Tests for injection attacks', 'High')
plan.add_test_category('Data Protection', 'Tests for data exposure', 'Medium')

# Добавление тестов
def test_auth_bypass():
    # Реализация теста
    return True

plan.add_test('Authentication', 'Admin Access Bypass', test_auth_bypass, True)
```

### 2. Мониторинг безопасности
```python
import logging
import time
from datetime import datetime

class SecurityMonitor:
    def __init__(self):
        self.logger = logging.getLogger('security_monitor')
        self.suspicious_activities = []
    
    def monitor_request(self, request_data):
        """Мониторинг подозрительных запросов"""
        suspicious_patterns = [
            'script',
            'javascript:',
            'union select',
            'drop table',
            'exec(',
            'eval(',
            '../',
            '..\\'
        ]
        
        request_string = str(request_data).lower()
        
        for pattern in suspicious_patterns:
            if pattern in request_string:
                self.log_suspicious_activity(request_data, pattern)
                return True
        
        return False
    
    def log_suspicious_activity(self, request_data, pattern):
        """Логирование подозрительной активности"""
        activity = {
            'timestamp': datetime.now(),
            'pattern': pattern,
            'request': request_data,
            'ip_address': request_data.get('ip', 'unknown'),
            'user_agent': request_data.get('user_agent', 'unknown')
        }
        
        self.suspicious_activities.append(activity)
        self.logger.warning(f"Suspicious activity detected: {pattern}")
    
    def generate_security_report(self):
        """Генерация отчета по безопасности"""
        report = {
            'timestamp': datetime.now(),
            'total_suspicious_activities': len(self.suspicious_activities),
            'activities_by_pattern': {},
            'activities_by_ip': {}
        }
        
        for activity in self.suspicious_activities:
            pattern = activity['pattern']
            ip = activity['ip_address']
            
            if pattern not in report['activities_by_pattern']:
                report['activities_by_pattern'][pattern] = 0
            report['activities_by_pattern'][pattern] += 1
            
            if ip not in report['activities_by_ip']:
                report['activities_by_ip'][ip] = 0
            report['activities_by_ip'][ip] += 1
        
        return report
```

## Полезные команды

**Q: Какие полезные команды для тестирования безопасности?**
A:

```bash
# Сканирование портов с nmap
nmap -sS -sV -O target.com
nmap --script vuln target.com
nmap -p 80,443,8080 target.com

# Тестирование SSL/TLS
nmap --script ssl-enum-ciphers -p 443 target.com
openssl s_client -connect target.com:443 -servername target.com

# Сканирование уязвимостей с Nikto
nikto -h target.com
nikto -h target.com -p 443 -ssl

# Тестирование с OWASP ZAP
zap-cli quick-scan --self-contained https://target.com
zap-cli active-scan https://target.com
zap-cli report -o security-report.html

# Тестирование с Burp Suite (через командную строку)
burp-rest-api --config-file=burp-config.json

# Сканирование зависимостей
npm audit
pip-audit
safety check
bundler audit

# Статический анализ кода
bandit -r /path/to/code
semgrep --config=auto /path/to/code
sonar-scanner

# Тестирование конфигурации
docker run --rm -v $(pwd):/app aquasec/trivy config /app
kubesec scan deployment.yaml

# Мониторинг безопасности
fail2ban-client status
logwatch --detail High
rkhunter --check

# Тестирование паролей
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt

# Анализ трафика
tcpdump -i eth0 -w capture.pcap
wireshark capture.pcap
tshark -r capture.pcap -Y "http.request.method==POST"
```
