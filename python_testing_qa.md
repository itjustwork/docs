# 🐍 Python Тестирование - Вопросы и Ответы

## Основы тестирования в Python

**Q: Что такое автоматическое тестирование в Python?**
A: Автоматическое тестирование в Python - это процесс написания кода для проверки правильности работы других частей кода. Это позволяет быстро находить ошибки и убеждаться, что изменения не ломают существующую функциональность.

**Преимущества автоматического тестирования:**
- ✅ Быстрое обнаружение ошибок
- ✅ Документирование поведения кода
- ✅ Уверенность при рефакторинге
- ✅ Улучшение дизайна кода
- ✅ Экономия времени в долгосрочной перспективе

**Q: Какие типы тестов бывают в Python?**
A:

| Тип теста | Что тестирует | Пример |
|-----------|---------------|---------|
| **Unit тесты** | Отдельные функции/методы | `test_add_numbers()` |
| **Integration тесты** | Взаимодействие компонентов | `test_database_connection()` |
| **Functional тесты** | Полные пользовательские сценарии | `test_user_registration()` |
| **Performance тесты** | Скорость и эффективность | `test_api_response_time()` |

## Модуль unittest

**Q: Как использовать встроенный модуль unittest?**
A: `unittest` - это встроенный модуль Python для написания тестов.

**Пример базового теста:**
```python
import unittest

def add_numbers(a, b):
    return a + b

class TestMathFunctions(unittest.TestCase):
    
    def test_add_positive_numbers(self):
        """Тест сложения положительных чисел"""
        result = add_numbers(2, 3)
        self.assertEqual(result, 5)
    
    def test_add_negative_numbers(self):
        """Тест сложения отрицательных чисел"""
        result = add_numbers(-1, -2)
        self.assertEqual(result, -3)
    
    def test_add_zero(self):
        """Тест сложения с нулем"""
        result = add_numbers(5, 0)
        self.assertEqual(result, 5)
    
    def test_add_floats(self):
        """Тест сложения дробных чисел"""
        result = add_numbers(1.5, 2.5)
        self.assertAlmostEqual(result, 4.0, places=1)

if __name__ == '__main__':
    unittest.main()
```

**Запуск тестов:**
```bash
python -m unittest test_math.py
python -m unittest test_math.TestMathFunctions.test_add_positive_numbers
```

**Q: Какие методы assert доступны в unittest?**
A:

```python
import unittest

class TestAssertions(unittest.TestCase):
    
    def test_assertions(self):
        # Проверка равенства
        self.assertEqual(2 + 2, 4)
        self.assertNotEqual(2 + 2, 5)
        
        # Проверка истинности
        self.assertTrue(True)
        self.assertFalse(False)
        
        # Проверка None
        self.assertIsNone(None)
        self.assertIsNotNone("not none")
        
        # Проверка вхождения
        self.assertIn(1, [1, 2, 3])
        self.assertNotIn(4, [1, 2, 3])
        
        # Проверка типов
        self.assertIsInstance("hello", str)
        self.assertNotIsInstance(123, str)
        
        # Проверка исключений
        with self.assertRaises(ValueError):
            int("not a number")
        
        # Проверка с погрешностью (для float)
        self.assertAlmostEqual(3.14159, 3.14, places=2)
        
        # Проверка регулярных выражений
        self.assertRegex("hello world", r"hello")
        self.assertNotRegex("hello world", r"bye")
```

## Pytest - современный фреймворк

**Q: Что такое pytest и чем он лучше unittest?**
A: Pytest - это современный фреймворк для тестирования в Python, который предлагает более простой и мощный синтаксис.

**Преимущества pytest:**
- ✅ Более простой синтаксис
- ✅ Лучшие сообщения об ошибках
- ✅ Фикстуры (fixtures)
- ✅ Параметризация тестов
- ✅ Плагины и расширения
- ✅ Автообнаружение тестов

**Установка pytest:**
```bash
pip install pytest
pip install pytest-cov  # для покрытия кода
pip install pytest-mock  # для мокирования
```

**Q: Как написать простой тест в pytest?**
A:

```python
# test_calculator.py

def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("Деление на ноль!")
    return a / b

# Тесты
def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_subtract():
    assert subtract(5, 3) == 2
    assert subtract(1, 1) == 0
    assert subtract(0, 5) == -5

def test_multiply():
    assert multiply(2, 3) == 6
    assert multiply(-2, 3) == -6
    assert multiply(0, 5) == 0

def test_divide():
    assert divide(6, 2) == 3
    assert divide(5, 2) == 2.5
    assert divide(-6, 2) == -3

def test_divide_by_zero():
    import pytest
    with pytest.raises(ValueError, match="Деление на ноль!"):
        divide(5, 0)
```

**Запуск тестов:**
```bash
pytest test_calculator.py
pytest test_calculator.py -v  # подробный вывод
pytest test_calculator.py::test_add  # конкретный тест
```

## Фикстуры в pytest

**Q: Что такое фикстуры и как их использовать?**
A: Фикстуры - это функции, которые предоставляют данные или объекты для тестов.

**Простой пример фикстур:**
```python
import pytest

@pytest.fixture
def sample_list():
    """Фикстура, возвращающая список"""
    return [1, 2, 3, 4, 5]

@pytest.fixture
def sample_dict():
    """Фикстура, возвращающая словарь"""
    return {"name": "John", "age": 30}

def test_list_operations(sample_list):
    """Тест с использованием фикстуры списка"""
    assert len(sample_list) == 5
    assert 3 in sample_list
    assert sum(sample_list) == 15

def test_dict_operations(sample_dict):
    """Тест с использованием фикстуры словаря"""
    assert sample_dict["name"] == "John"
    assert sample_dict["age"] == 30
    assert len(sample_dict) == 2
```

**Фикстуры с настройкой и очисткой:**
```python
import pytest
import tempfile
import os

@pytest.fixture
def temp_file():
    """Фикстура с настройкой и очисткой"""
    # Настройка
    temp = tempfile.NamedTemporaryFile(delete=False)
    temp.write(b"test data")
    temp.close()
    
    yield temp.name  # Возвращаем имя файла
    
    # Очистка
    os.unlink(temp.name)

def test_file_operations(temp_file):
    """Тест работы с временным файлом"""
    with open(temp_file, 'r') as f:
        content = f.read()
    assert content == "test data"
```

**Фикстуры с параметрами:**
```python
import pytest

@pytest.fixture(params=[1, 2, 3, 4, 5])
def number(request):
    """Фикстура с параметрами"""
    return request.param

def test_number_is_positive(number):
    """Тест будет запущен 5 раз с разными числами"""
    assert number > 0
    assert number <= 5
```

## Параметризация тестов

**Q: Как параметризовать тесты в pytest?**
A: Параметризация позволяет запускать один тест с разными наборами данных.

**Простая параметризация:**
```python
import pytest

@pytest.mark.parametrize("a, b, expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
    (10, 20, 30),
])
def test_addition(a, b, expected):
    assert a + b == expected

@pytest.mark.parametrize("input_str, expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("", ""),
    ("Python", "PYTHON"),
])
def test_uppercase(input_str, expected):
    assert input_str.upper() == expected
```

**Множественная параметризация:**
```python
import pytest

@pytest.mark.parametrize("x", [1, 2, 3])
@pytest.mark.parametrize("y", [10, 20])
def test_multiple_params(x, y):
    """Тест будет запущен 6 раз (3 x 2)"""
    assert x * y > 0
    assert x * y <= 60
```

**Параметризация с ID:**
```python
import pytest

@pytest.mark.parametrize("test_input,expected", [
    ("3+5", 8),
    ("2+4", 6),
    ("6*9", 54),
], ids=["addition_1", "addition_2", "multiplication"])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

## Мокирование (Mocking)

**Q: Как использовать мокирование в тестах?**
A: Мокирование позволяет заменять реальные объекты на имитации для изоляции тестов.

**Использование unittest.mock:**
```python
from unittest.mock import Mock, patch, MagicMock
import pytest

# Простой мок
def test_mock_basic():
    mock = Mock()
    mock.some_method.return_value = 42
    
    assert mock.some_method() == 42
    mock.some_method.assert_called_once()

# Мокирование функции
def get_user_data(user_id):
    # В реальности здесь был бы запрос к API
    pass

@patch('module.get_user_data')
def test_with_mocked_function(mock_get_user):
    mock_get_user.return_value = {"id": 1, "name": "John"}
    
    result = get_user_data(1)
    assert result["name"] == "John"
    mock_get_user.assert_called_once_with(1)
```

**Мокирование с pytest-mock:**
```python
import pytest

def test_with_pytest_mock(mocker):
    # Мокирование функции
    mock_func = mocker.patch('module.some_function')
    mock_func.return_value = "mocked result"
    
    # Мокирование класса
    mock_class = mocker.patch('module.SomeClass')
    mock_instance = mock_class.return_value
    mock_instance.method.return_value = "mocked method"
    
    # Проверка вызовов
    assert mock_func.called
    assert mock_instance.method.called
```

**Мокирование внешних API:**
```python
import requests
from unittest.mock import patch

def get_weather(city):
    response = requests.get(f"https://api.weather.com/{city}")
    return response.json()

@patch('requests.get')
def test_get_weather(mock_get):
    # Настройка мока
    mock_response = Mock()
    mock_response.json.return_value = {"temperature": 25, "city": "Moscow"}
    mock_get.return_value = mock_response
    
    # Выполнение теста
    result = get_weather("Moscow")
    
    # Проверки
    assert result["temperature"] == 25
    assert result["city"] == "Moscow"
    mock_get.assert_called_once_with("https://api.weather.com/Moscow")
```

## Тестирование классов

**Q: Как тестировать классы в Python?**
A:

**Пример класса для тестирования:**
```python
class Calculator:
    def __init__(self):
        self.history = []
    
    def add(self, a, b):
        result = a + b
        self.history.append(f"{a} + {b} = {result}")
        return result
    
    def subtract(self, a, b):
        result = a - b
        self.history.append(f"{a} - {b} = {result}")
        return result
    
    def get_history(self):
        return self.history
    
    def clear_history(self):
        self.history.clear()
```

**Тесты для класса:**
```python
import pytest

class TestCalculator:
    
    @pytest.fixture
    def calculator(self):
        """Фикстура для создания экземпляра калькулятора"""
        return Calculator()
    
    def test_add(self, calculator):
        """Тест метода сложения"""
        result = calculator.add(2, 3)
        assert result == 5
        assert len(calculator.history) == 1
        assert calculator.history[0] == "2 + 3 = 5"
    
    def test_subtract(self, calculator):
        """Тест метода вычитания"""
        result = calculator.subtract(5, 3)
        assert result == 2
        assert len(calculator.history) == 1
        assert calculator.history[0] == "5 - 3 = 2"
    
    def test_history(self, calculator):
        """Тест истории операций"""
        calculator.add(1, 2)
        calculator.subtract(5, 3)
        
        history = calculator.get_history()
        assert len(history) == 2
        assert history[0] == "1 + 2 = 3"
        assert history[1] == "5 - 3 = 2"
    
    def test_clear_history(self, calculator):
        """Тест очистки истории"""
        calculator.add(1, 2)
        calculator.clear_history()
        assert len(calculator.history) == 0
```

## Тестирование исключений

**Q: Как тестировать исключения в Python?**
A:

**Тестирование исключений в pytest:**
```python
import pytest

def divide(a, b):
    if b == 0:
        raise ValueError("Деление на ноль!")
    return a / b

def test_divide_by_zero():
    """Тест исключения при делении на ноль"""
    with pytest.raises(ValueError) as exc_info:
        divide(5, 0)
    
    assert str(exc_info.value) == "Деление на ноль!"

def test_divide_by_zero_message():
    """Тест с проверкой сообщения исключения"""
    with pytest.raises(ValueError, match="Деление на ноль!"):
        divide(5, 0)

def test_divide_success():
    """Тест успешного деления"""
    result = divide(6, 2)
    assert result == 3
```

**Тестирование пользовательских исключений:**
```python
class InsufficientFundsError(Exception):
    pass

class BankAccount:
    def __init__(self, balance=0):
        self.balance = balance
    
    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError(f"Недостаточно средств. Баланс: {self.balance}")
        self.balance -= amount
        return amount

def test_insufficient_funds():
    """Тест исключения недостатка средств"""
    account = BankAccount(100)
    
    with pytest.raises(InsufficientFundsError) as exc_info:
        account.withdraw(200)
    
    assert "Недостаточно средств" in str(exc_info.value)
    assert account.balance == 100  # Баланс не изменился
```

## Покрытие кода (Code Coverage)

**Q: Как измерять покрытие кода тестами?**
A: Покрытие кода показывает, какая часть кода выполняется при запуске тестов.

**Установка coverage:**
```bash
pip install coverage
pip install pytest-cov
```

**Измерение покрытия с pytest-cov:**
```bash
# Запуск тестов с измерением покрытия
pytest --cov=my_module tests/
pytest --cov=my_module --cov-report=html tests/
pytest --cov=my_module --cov-report=term-missing tests/
```

**Пример кода с тестами:**
```python
# calculator.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("Деление на ноль!")
    return a / b

# test_calculator.py
import pytest
from calculator import add, subtract, multiply, divide

def test_add():
    assert add(2, 3) == 5

def test_subtract():
    assert subtract(5, 3) == 2

def test_multiply():
    assert multiply(2, 3) == 6

def test_divide():
    assert divide(6, 2) == 3

def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(5, 0)
```

**Запуск с покрытием:**
```bash
pytest --cov=calculator test_calculator.py --cov-report=term-missing
```

## Интеграционные тесты

**Q: Как писать интеграционные тесты?**
A: Интеграционные тесты проверяют взаимодействие между компонентами.

**Пример интеграционного теста с базой данных:**
```python
import pytest
import sqlite3
from unittest.mock import patch

class UserService:
    def __init__(self, db_connection):
        self.db = db_connection
    
    def create_user(self, username, email):
        cursor = self.db.cursor()
        cursor.execute(
            "INSERT INTO users (username, email) VALUES (?, ?)",
            (username, email)
        )
        self.db.commit()
        return cursor.lastrowid
    
    def get_user(self, user_id):
        cursor = self.db.cursor()
        cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
        return cursor.fetchone()

@pytest.fixture
def db_connection():
    """Фикстура для создания тестовой базы данных"""
    conn = sqlite3.connect(':memory:')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE users (
            id INTEGER PRIMARY KEY,
            username TEXT NOT NULL,
            email TEXT NOT NULL
        )
    ''')
    conn.commit()
    yield conn
    conn.close()

def test_user_creation_and_retrieval(db_connection):
    """Интеграционный тест создания и получения пользователя"""
    service = UserService(db_connection)
    
    # Создание пользователя
    user_id = service.create_user("john_doe", "john@example.com")
    assert user_id is not None
    
    # Получение пользователя
    user = service.get_user(user_id)
    assert user is not None
    assert user[1] == "john_doe"  # username
    assert user[2] == "john@example.com"  # email
```

## Тестирование веб-приложений

**Q: Как тестировать веб-приложения на Python?**
A:

**Тестирование Flask приложения:**
```python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route('/api/users', methods=['GET'])
def get_users():
    return jsonify({"users": ["John", "Jane"]})

@app.route('/api/users', methods=['POST'])
def create_user():
    data = request.get_json()
    if not data or 'name' not in data:
        return jsonify({"error": "Name is required"}), 400
    return jsonify({"message": f"User {data['name']} created"}), 201

# Тесты
import pytest
from app import app

@pytest.fixture
def client():
    """Фикстура для тестового клиента"""
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_get_users(client):
    """Тест получения списка пользователей"""
    response = client.get('/api/users')
    assert response.status_code == 200
    data = response.get_json()
    assert 'users' in data
    assert len(data['users']) == 2

def test_create_user_success(client):
    """Тест успешного создания пользователя"""
    response = client.post('/api/users', 
                          json={'name': 'Alice'})
    assert response.status_code == 201
    data = response.get_json()
    assert 'User Alice created' in data['message']

def test_create_user_missing_name(client):
    """Тест создания пользователя без имени"""
    response = client.post('/api/users', 
                          json={})
    assert response.status_code == 400
    data = response.get_json()
    assert data['error'] == 'Name is required'
```

## Лучшие практики

**Q: Какие лучшие практики тестирования в Python?**
A:

**1. Именование тестов:**
```python
# Хорошо
def test_user_can_login_with_valid_credentials():
    pass

def test_user_cannot_login_with_invalid_password():
    pass

# Плохо
def test_login():
    pass
```

**2. Структура тестов (AAA - Arrange, Act, Assert):**
```python
def test_calculate_discount():
    # Arrange (подготовка)
    price = 100
    discount_percent = 20
    
    # Act (действие)
    final_price = calculate_discount(price, discount_percent)
    
    # Assert (проверка)
    assert final_price == 80
```

**3. Использование фикстур:**
```python
@pytest.fixture
def sample_user():
    return {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
    }

def test_user_operations(sample_user):
    # Используем фикстуру вместо создания данных в тесте
    assert sample_user["name"] == "John Doe"
```

**4. Изоляция тестов:**
```python
# Каждый тест должен быть независимым
def test_user_creation():
    user = create_user("test", "test@example.com")
    assert user.name == "test"

def test_user_deletion():
    user = create_user("test", "test@example.com")
    delete_user(user.id)
    assert get_user(user.id) is None
```

**5. Тестирование граничных случаев:**
```python
def test_edge_cases():
    # Пустые значения
    assert process_data("") == ""
    
    # Очень большие значения
    assert process_data("a" * 1000) is not None
    
    # Специальные символы
    assert process_data("!@#$%^&*()") is not None
```

**6. Использование параметризации:**
```python
@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("", ""),
    ("123", "123"),
])
def test_uppercase(input, expected):
    assert input.upper() == expected
```

## Полезные команды pytest

**Q: Какие полезные команды pytest?**
A:

```bash
# Запуск всех тестов
pytest

# Подробный вывод
pytest -v

# Очень подробный вывод
pytest -vv

# Запуск конкретного теста
pytest test_file.py::test_function

# Запуск тестов по маркеру
pytest -m "slow"

# Пропуск тестов
pytest -x  # остановка при первой ошибке
pytest --maxfail=2  # остановка после 2 ошибок

# Параллельный запуск
pytest -n auto

# Покрытие кода
pytest --cov=my_module --cov-report=html

# Показать локальные переменные при ошибке
pytest -l

# Запуск тестов в случайном порядке
pytest --random-order

# Показать самые медленные тесты
pytest --durations=10
```

## Маркеры в pytest

**Q: Как использовать маркеры в pytest?**
A:

**Определение маркеров:**
```python
import pytest

@pytest.mark.slow
def test_slow_operation():
    """Медленный тест"""
    import time
    time.sleep(2)
    assert True

@pytest.mark.integration
def test_database_connection():
    """Интеграционный тест"""
    assert True

@pytest.mark.unit
def test_fast_function():
    """Быстрый unit тест"""
    assert True
```

**Запуск тестов по маркерам:**
```bash
# Запуск только медленных тестов
pytest -m slow

# Запуск всех тестов кроме медленных
pytest -m "not slow"

# Запуск интеграционных тестов
pytest -m integration

# Запуск unit тестов
pytest -m unit
```

**Настройка маркеров в pytest.ini:**
```ini
[tool:pytest]
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks tests as integration tests
    unit: marks tests as unit tests
```
