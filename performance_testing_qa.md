# ⚡ Нагрузочное тестирование - Вопросы и Ответы

## Основы нагрузочного тестирования

**Q: Что такое нагрузочное тестирование и зачем оно нужно?**
A: Нагрузочное тестирование - это процесс проверки производительности системы под различными нагрузками для определения её предельных возможностей и узких мест.

**Цели нагрузочного тестирования:**
- ✅ Определение максимальной производительности системы
- ✅ Выявление узких мест и ограничений
- ✅ Проверка стабильности под нагрузкой
- ✅ Валидация требований к производительности
- ✅ Оптимизация производительности
- ✅ Планирование масштабирования

**Q: Какие типы нагрузочного тестирования существуют?**
A:

| Тип тестирования | Цель | Пример |
|------------------|------|---------|
| **Load Testing** | Проверка производительности под ожидаемой нагрузкой | 1000 пользователей одновременно |
| **Stress Testing** | Определение предела производительности | Увеличение нагрузки до отказа |
| **Spike Testing** | Проверка реакции на резкие скачки нагрузки | Внезапное увеличение с 100 до 1000 пользователей |
| **Soak Testing** | Проверка стабильности при длительной нагрузке | 24 часа непрерывной работы |
| **Volume Testing** | Проверка с большими объемами данных | База данных с миллионами записей |
| **Scalability Testing** | Проверка масштабируемости | Добавление ресурсов и проверка улучшений |

## Инструменты нагрузочного тестирования

**Q: Какие популярные инструменты для нагрузочного тестирования?**
A:

### Apache JMeter
**Описание:** Бесплатный инструмент для нагрузочного тестирования с графическим интерфейсом.

**Пример JMeter теста:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<jmeterTestPlan version="1.2" properties="5.0">
  <hashTree>
    <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="Web Application Load Test">
      <elementProp name="TestPlan.arguments" elementType="Arguments">
        <collectionProp name="Arguments.arguments"/>
      </elementProp>
      <boolProp name="TestPlan.functional_mode">false</boolProp>
      <stringProp name="TestPlan.comments"></stringProp>
      <boolProp name="TestPlan.tearDown_on_shutdown">true</boolProp>
      <boolProp name="TestPlan.serialize_threadgroups">false</boolProp>
    </TestPlan>
    <hashTree>
      <ThreadGroup guiclass="ThreadGroupGui" testclass="ThreadGroup" testname="User Group">
        <elementProp name="ThreadGroup.main_controller" elementType="LoopController">
          <boolProp name="LoopController.continue_forever">false</boolProp>
          <stringProp name="LoopController.loops">10</stringProp>
        </elementProp>
        <stringProp name="ThreadGroup.num_threads">100</stringProp>
        <stringProp name="ThreadGroup.ramp_time">60</stringProp>
        <boolProp name="ThreadGroup.scheduler">false</boolProp>
        <stringProp name="ThreadGroup.duration"></stringProp>
        <stringProp name="ThreadGroup.delay"></stringProp>
      </ThreadGroup>
      <hashTree>
        <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="Home Page">
          <elementProp name="HTTPsampler.Arguments" elementType="Arguments">
            <collectionProp name="Arguments.arguments"/>
          </elementProp>
          <stringProp name="HTTPSampler.domain">example.com</stringProp>
          <stringProp name="HTTPSampler.port">80</stringProp>
          <stringProp name="HTTPSampler.protocol">http</stringProp>
          <stringProp name="HTTPSampler.path">/</stringProp>
          <stringProp name="HTTPSampler.method">GET</stringProp>
          <boolProp name="HTTPSampler.follow_redirects">true</boolProp>
          <boolProp name="HTTPSampler.auto_redirects">false</boolProp>
          <boolProp name="HTTPSampler.use_keepalive">true</boolProp>
          <boolProp name="HTTPSampler.DO_MULTIPART_POST">false</boolProp>
        </HTTPSamplerProxy>
        <hashTree>
          <ResponseAssertion guiclass="AssertionGui" testclass="ResponseAssertion" testname="Response Code">
            <collectionProp name="Asserion.test_strings">
              <stringProp name="49586">200</stringProp>
            </collectionProp>
            <stringProp name="Assertion.test_field">Assertion.response_code</stringProp>
            <boolProp name="Assertion.assume_success">false</boolProp>
            <intProp name="Assertion.test_type">8</intProp>
          </ResponseAssertion>
          <hashTree/>
          <DurationAssertion guiclass="DurationAssertionGui" testclass="DurationAssertion" testname="Response Time">
            <stringProp name="DurationAssertion.duration">2000</stringProp>
          </DurationAssertion>
          <hashTree/>
        </hashTree>
      </hashTree>
    </hashTree>
  </hashTree>
</jmeterTestPlan>
```

**Команды для запуска JMeter:**
```bash
# Запуск JMeter в GUI режиме
jmeter

# Запуск JMeter в командной строке
jmeter -n -t test-plan.jmx -l results.jtl -e -o report/

# Запуск с параметрами
jmeter -n -t test-plan.jmx -l results.jtl -Jthreads=100 -Jrampup=60
```

### K6
**Описание:** Современный инструмент для нагрузочного тестирования с JavaScript синтаксисом.

**Пример K6 скрипта:**
```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

// Кастомные метрики
const errorRate = new Rate('errors');

// Опции теста
export const options = {
  stages: [
    { duration: '2m', target: 100 }, // Ramp-up до 100 пользователей
    { duration: '5m', target: 100 }, // Держим 100 пользователей
    { duration: '2m', target: 200 }, // Ramp-up до 200 пользователей
    { duration: '5m', target: 200 }, // Держим 200 пользователей
    { duration: '2m', target: 0 },   // Ramp-down до 0
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% запросов должны быть быстрее 500ms
    http_req_failed: ['rate<0.1'],    // Меньше 10% ошибок
    errors: ['rate<0.1'],             // Меньше 10% ошибок
  },
};

// Настройка пользователя
export function setup() {
  // Логин и получение токена
  const loginRes = http.post('https://api.example.com/auth/login', {
    email: 'user@example.com',
    password: 'password123',
  });
  
  return { authToken: loginRes.json('token') };
}

// Основная функция теста
export default function(data) {
  const params = {
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${data.authToken}`,
    },
  };

  // Тест получения списка пользователей
  const usersRes = http.get('https://api.example.com/users', params);
  
  check(usersRes, {
    'users status is 200': (r) => r.status === 200,
    'users response time < 500ms': (r) => r.timings.duration < 500,
    'users response has data': (r) => r.json().length > 0,
  }) || errorRate.add(1);

  // Тест получения конкретного пользователя
  const userRes = http.get('https://api.example.com/users/1', params);
  
  check(userRes, {
    'user status is 200': (r) => r.status === 200,
    'user response time < 300ms': (r) => r.timings.duration < 300,
    'user has correct id': (r) => r.json('id') === 1,
  }) || errorRate.add(1);

  // Тест создания пользователя
  const createUserRes = http.post('https://api.example.com/users', JSON.stringify({
    name: `User ${__VU}`,
    email: `user${__VU}@example.com`,
  }), params);
  
  check(createUserRes, {
    'create user status is 201': (r) => r.status === 201,
    'create user response time < 1000ms': (r) => r.timings.duration < 1000,
    'created user has id': (r) => r.json('id') !== undefined,
  }) || errorRate.add(1);

  sleep(1);
}

// Функция очистки
export function teardown(data) {
  console.log('Test completed');
}
```

**Запуск K6:**
```bash
# Запуск теста
k6 run script.js

# Запуск с параметрами
k6 run --vus 100 --duration 5m script.js

# Запуск с переменными окружения
k6 run -e BASE_URL=https://api.example.com script.js

# Запуск в облаке
k6 cloud script.js
```

### Artillery
**Описание:** Node.js инструмент для нагрузочного тестирования с YAML конфигурацией.

**Пример Artillery конфигурации:**
```yaml
config:
  target: 'https://api.example.com'
  phases:
    - duration: 60
      arrivalRate: 10
      name: "Warm up"
    - duration: 300
      arrivalRate: 50
      name: "Normal load"
    - duration: 120
      arrivalRate: 100
      name: "Peak load"
    - duration: 60
      arrivalRate: 10
      name: "Cool down"
  defaults:
    headers:
      Content-Type: 'application/json'
  processor: "./functions.js"
  variables:
    email: "user@example.com"
    password: "password123"

scenarios:
  - name: "API Load Test"
    weight: 1
    flow:
      - post:
          url: "/auth/login"
          json:
            email: "{{ email }}"
            password: "{{ password }}"
          capture:
            - json: "$.token"
              as: "authToken"
      
      - get:
          url: "/users"
          headers:
            Authorization: "Bearer {{ authToken }}"
          expect:
            - statusCode: 200
            - contentType: json
      
      - get:
          url: "/users/1"
          headers:
            Authorization: "Bearer {{ authToken }}"
          expect:
            - statusCode: 200
            - contentType: json
      
      - post:
          url: "/users"
          json:
            name: "{{ $randomString() }}"
            email: "{{ $randomEmail() }}"
          headers:
            Authorization: "Bearer {{ authToken }}"
          expect:
            - statusCode: 201
            - contentType: json
      
      - think: 2

  - name: "Heavy Load Test"
    weight: 0.3
    flow:
      - get:
          url: "/users"
          headers:
            Authorization: "Bearer {{ authToken }}"
      
      - get:
          url: "/users?page=1&limit=100"
          headers:
            Authorization: "Bearer {{ authToken }}"
      
      - think: 5
```

**Функции для Artillery:**
```javascript
// functions.js
const faker = require('faker');

function generateRandomUser() {
  return {
    name: faker.name.findName(),
    email: faker.internet.email(),
    password: faker.internet.password()
  };
}

function validateResponse(response, context, events, next) {
  if (response.statusCode !== 200) {
    console.error(`Request failed with status: ${response.statusCode}`);
  }
  return next();
}

module.exports = {
  generateRandomUser,
  validateResponse
};
```

**Запуск Artillery:**
```bash
# Установка Artillery
npm install -g artillery

# Запуск теста
artillery run config.yml

# Запуск с отчетами
artillery run --output results.json config.yml
artillery report results.json

# Запуск в режиме реального времени
artillery run --insecure config.yml
```

## Типы нагрузочного тестирования

**Q: Как проводить различные типы нагрузочного тестирования?**
A:

### Load Testing (Нагрузочное тестирование)
```javascript
// load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 50 },   // Ramp-up до 50 пользователей
    { duration: '10m', target: 50 },  // Держим 50 пользователей
    { duration: '2m', target: 0 },    // Ramp-down
  ],
  thresholds: {
    http_req_duration: ['p(95)<1000'], // 95% запросов < 1 секунды
    http_req_failed: ['rate<0.05'],    // Меньше 5% ошибок
  },
};

export default function() {
  const response = http.get('https://api.example.com/users');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 1s': (r) => r.timings.duration < 1000,
  });
  
  sleep(1);
}
```

### Stress Testing (Стресс-тестирование)
```javascript
// stress-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 },  // Нормальная нагрузка
    { duration: '5m', target: 100 },  // Стабильная нагрузка
    { duration: '2m', target: 200 },  // Увеличение нагрузки
    { duration: '5m', target: 200 },  // Высокая нагрузка
    { duration: '2m', target: 300 },  // Критическая нагрузка
    { duration: '5m', target: 300 },  // Максимальная нагрузка
    { duration: '2m', target: 0 },    // Снижение нагрузки
  ],
  thresholds: {
    http_req_duration: ['p(95)<2000'], // 95% запросов < 2 секунд
    http_req_failed: ['rate<0.2'],     // Меньше 20% ошибок
  },
};

export default function() {
  const response = http.get('https://api.example.com/users');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 2s': (r) => r.timings.duration < 2000,
  });
  
  sleep(1);
}
```

### Spike Testing (Тестирование пиковых нагрузок)
```javascript
// spike-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 50 },   // Нормальная нагрузка
    { duration: '30s', target: 500 }, // Резкий скачок
    { duration: '2m', target: 500 },  // Высокая нагрузка
    { duration: '30s', target: 50 },  // Резкое снижение
    { duration: '2m', target: 50 },   // Возврат к нормальной нагрузке
  ],
  thresholds: {
    http_req_duration: ['p(95)<3000'], // 95% запросов < 3 секунд
    http_req_failed: ['rate<0.3'],     // Меньше 30% ошибок
  },
};

export default function() {
  const response = http.get('https://api.example.com/users');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 3s': (r) => r.timings.duration < 3000,
  });
  
  sleep(1);
}
```

### Soak Testing (Тестирование на выносливость)
```javascript
// soak-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '10m', target: 50 },  // Ramp-up
    { duration: '2h', target: 50 },   // Длительная нагрузка
    { duration: '10m', target: 0 },   // Ramp-down
  ],
  thresholds: {
    http_req_duration: ['p(95)<1000'], // 95% запросов < 1 секунды
    http_req_failed: ['rate<0.05'],    // Меньше 5% ошибок
    http_reqs: ['rate>45'],            // Минимум 45 запросов в секунду
  },
};

export default function() {
  const response = http.get('https://api.example.com/users');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 1s': (r) => r.timings.duration < 1000,
  });
  
  sleep(1);
}
```

## Мониторинг и метрики

**Q: Какие метрики важны при нагрузочном тестировании?**
A:

### Основные метрики производительности
```javascript
// metrics-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate, Trend, Counter } from 'k6/metrics';

// Кастомные метрики
const errorRate = new Rate('errors');
const responseTime = new Trend('response_time');
const requestCount = new Counter('requests');

export const options = {
  vus: 100,
  duration: '5m',
  thresholds: {
    http_req_duration: ['p(50)<500', 'p(95)<1000', 'p(99)<2000'],
    http_req_failed: ['rate<0.1'],
    http_reqs: ['rate>90'],
    errors: ['rate<0.1'],
    response_time: ['p(95)<1000'],
  },
};

export default function() {
  const startTime = Date.now();
  const response = http.get('https://api.example.com/users');
  const endTime = Date.now();
  
  // Запись метрик
  responseTime.add(endTime - startTime);
  requestCount.add(1);
  
  const success = check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 1s': (r) => r.timings.duration < 1000,
    'response has data': (r) => r.json().length > 0,
  });
  
  if (!success) {
    errorRate.add(1);
  }
  
  sleep(1);
}

// Функция для вывода дополнительных метрик
export function handleSummary(data) {
  return {
    'summary.json': JSON.stringify(data),
    stdout: `
      Test Results:
      - Total requests: ${data.metrics.http_reqs.values.count}
      - Failed requests: ${data.metrics.http_req_failed.values.passes}
      - Average response time: ${data.metrics.http_req_duration.values.avg}ms
      - 95th percentile: ${data.metrics.http_req_duration.values['p(95)']}ms
      - Requests per second: ${data.metrics.http_reqs.values.rate}
    `,
  };
}
```

### Мониторинг системных ресурсов
```bash
# Мониторинг CPU и памяти
top -p $(pgrep -f "your-application")

# Мониторинг сети
iftop -i eth0

# Мониторинг диска
iostat -x 1

# Мониторинг с помощью htop
htop

# Мониторинг с помощью glances
glances

# Мониторинг с помощью Prometheus + Grafana
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'web-application'
    static_configs:
      - targets: ['localhost:8080']
    metrics_path: '/metrics'
```

## Анализ результатов

**Q: Как анализировать результаты нагрузочного тестирования?**
A:

### Анализ с помощью Python
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Загрузка данных из JMeter
def analyze_jmeter_results(file_path):
    # Чтение CSV файла с результатами JMeter
    df = pd.read_csv(file_path)
    
    # Анализ времени отклика
    print("=== Анализ времени отклика ===")
    print(f"Среднее время: {df['elapsed'].mean():.2f}ms")
    print(f"Медиана: {df['elapsed'].median():.2f}ms")
    print(f"95-й процентиль: {df['elapsed'].quantile(0.95):.2f}ms")
    print(f"99-й процентиль: {df['elapsed'].quantile(0.99):.2f}ms")
    
    # Анализ ошибок
    print("\n=== Анализ ошибок ===")
    error_count = len(df[df['success'] == False])
    total_count = len(df)
    error_rate = (error_count / total_count) * 100
    print(f"Всего запросов: {total_count}")
    print(f"Ошибок: {error_count}")
    print(f"Процент ошибок: {error_rate:.2f}%")
    
    # Визуализация
    plt.figure(figsize=(15, 10))
    
    # График времени отклика
    plt.subplot(2, 2, 1)
    plt.hist(df['elapsed'], bins=50, alpha=0.7)
    plt.title('Распределение времени отклика')
    plt.xlabel('Время (мс)')
    plt.ylabel('Количество запросов')
    
    # График времени отклика по времени
    plt.subplot(2, 2, 2)
    plt.scatter(df['timeStamp'], df['elapsed'], alpha=0.5)
    plt.title('Время отклика по времени')
    plt.xlabel('Время')
    plt.ylabel('Время отклика (мс)')
    
    # График успешных/неуспешных запросов
    plt.subplot(2, 2, 3)
    success_counts = df['success'].value_counts()
    plt.pie(success_counts.values, labels=['Успешно', 'Ошибка'], autopct='%1.1f%%')
    plt.title('Соотношение успешных и неуспешных запросов')
    
    # График пропускной способности
    plt.subplot(2, 2, 4)
    df['timestamp_sec'] = df['timeStamp'] // 1000
    requests_per_sec = df.groupby('timestamp_sec').size()
    plt.plot(requests_per_sec.index, requests_per_sec.values)
    plt.title('Запросов в секунду')
    plt.xlabel('Время (секунды)')
    plt.ylabel('Запросов/сек')
    
    plt.tight_layout()
    plt.show()

# Анализ результатов K6
def analyze_k6_results(file_path):
    import json
    
    with open(file_path, 'r') as f:
        data = json.load(f)
    
    metrics = data['metrics']
    
    print("=== Результаты K6 ===")
    print(f"Всего запросов: {metrics['http_reqs']['values']['count']}")
    print(f"Запросов в секунду: {metrics['http_reqs']['values']['rate']:.2f}")
    print(f"Среднее время отклика: {metrics['http_req_duration']['values']['avg']:.2f}ms")
    print(f"95-й процентиль: {metrics['http_req_duration']['values']['p(95)']:.2f}ms")
    print(f"Процент ошибок: {metrics['http_req_failed']['values']['rate']*100:.2f}%")

# Использование
analyze_jmeter_results('results.csv')
analyze_k6_results('k6-results.json')
```

### Автоматический анализ с помощью скриптов
```bash
#!/bin/bash
# analyze-results.sh

echo "=== Анализ результатов нагрузочного тестирования ==="

# Анализ JMeter результатов
if [ -f "results.csv" ]; then
    echo "Анализ JMeter результатов:"
    awk -F',' '
    NR>1 {
        total++;
        elapsed_sum += $2;
        if ($8 == "false") errors++;
    }
    END {
        avg = elapsed_sum / total;
        error_rate = (errors / total) * 100;
        print "Всего запросов: " total;
        print "Среднее время отклика: " avg "ms";
        print "Ошибок: " errors;
        print "Процент ошибок: " error_rate "%";
    }' results.csv
fi

# Анализ K6 результатов
if [ -f "k6-results.json" ]; then
    echo "Анализ K6 результатов:"
    jq -r '
    .metrics.http_reqs.values | 
    "Всего запросов: \(.count)\nЗапросов в секунду: \(.rate)\n" +
    "Среднее время отклика: \(.avg)ms\n95-й процентиль: \(.["p(95)"])ms"
    ' k6-results.json
fi

# Создание отчета
echo "Создание отчета..."
cat > report.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>Отчет нагрузочного тестирования</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .metric { background: #f5f5f5; padding: 10px; margin: 10px 0; border-radius: 5px; }
        .success { color: green; }
        .warning { color: orange; }
        .error { color: red; }
    </style>
</head>
<body>
    <h1>Отчет нагрузочного тестирования</h1>
    <div class="metric">
        <h3>Основные метрики</h3>
        <p>Дата тестирования: $(date)</p>
        <p>Длительность теста: $DURATION</p>
        <p>Количество пользователей: $USERS</p>
    </div>
</body>
</html>
EOF

echo "Отчет создан: report.html"
```

## Лучшие практики

**Q: Какие лучшие практики нагрузочного тестирования?**
A:

### 1. Планирование тестирования
```python
# test-planning.py
class LoadTestPlan:
    def __init__(self):
        self.test_scenarios = []
        self.environment = {}
        self.metrics = {}
    
    def add_scenario(self, name, users, duration, ramp_up):
        """Добавление сценария тестирования"""
        scenario = {
            'name': name,
            'users': users,
            'duration': duration,
            'ramp_up': ramp_up,
            'thresholds': {
                'response_time_p95': 1000,  # 95% запросов < 1 секунды
                'error_rate': 0.05,         # Меньше 5% ошибок
                'throughput': 100           # Минимум 100 запросов/сек
            }
        }
        self.test_scenarios.append(scenario)
    
    def set_environment(self, base_url, headers=None):
        """Настройка окружения"""
        self.environment = {
            'base_url': base_url,
            'headers': headers or {},
            'timeout': 30,
            'think_time': 1
        }
    
    def generate_k6_script(self):
        """Генерация K6 скрипта"""
        script = """
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
"""
        
        for scenario in self.test_scenarios:
            script += f"    {{ duration: '{scenario['ramp_up']}', target: {scenario['users']} }},\n"
            script += f"    {{ duration: '{scenario['duration']}', target: {scenario['users']} }},\n"
        
        script += "  ],\n  thresholds: {\n"
        script += "    http_req_duration: ['p(95)<1000'],\n"
        script += "    http_req_failed: ['rate<0.05'],\n"
        script += "  },\n};\n"
        
        script += """
export default function() {
  const response = http.get('""" + self.environment['base_url'] + """');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 1s': (r) => r.timings.duration < 1000,
  });
  
  sleep(""" + str(self.environment['think_time']) + """);
}
"""
        return script

# Использование
plan = LoadTestPlan()
plan.set_environment('https://api.example.com')
plan.add_scenario('Normal Load', 50, '5m', '2m')
plan.add_scenario('Peak Load', 100, '3m', '1m')

with open('generated-test.js', 'w') as f:
    f.write(plan.generate_k6_script())
```

### 2. Мониторинг во время тестирования
```bash
#!/bin/bash
# monitor-test.sh

echo "Запуск мониторинга во время нагрузочного тестирования..."

# Мониторинг CPU
top -b -n 1 | grep "Cpu(s)" > cpu_usage.log &

# Мониторинг памяти
free -m | grep "Mem:" > memory_usage.log &

# Мониторинг сети
netstat -i | grep eth0 > network_usage.log &

# Мониторинг диска
iostat -x 1 10 > disk_usage.log &

# Мониторинг процессов
ps aux | grep "your-application" > process_usage.log &

echo "Мониторинг запущен. Результаты сохраняются в лог-файлы."
```

### 3. Автоматизация тестирования
```yaml
# .github/workflows/load-test.yml
name: Load Testing

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  load-test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '16'
    
    - name: Install dependencies
      run: npm install
    
    - name: Start application
      run: npm start &
    
    - name: Wait for application
      run: sleep 30
    
    - name: Run load test
      run: k6 run load-test.js
    
    - name: Upload results
      uses: actions/upload-artifact@v2
      with:
        name: load-test-results
        path: k6-results.json
    
    - name: Generate report
      run: |
        k6 run --out json=k6-results.json load-test.js
        python analyze_results.py
```

## Полезные команды

**Q: Какие полезные команды для нагрузочного тестирования?**
A:

```bash
# JMeter команды
jmeter -n -t test-plan.jmx -l results.jtl -e -o report/
jmeter -n -t test-plan.jmx -Jthreads=100 -Jrampup=60 -Jduration=300

# K6 команды
k6 run script.js
k6 run --vus 100 --duration 5m script.js
k6 cloud script.js
k6 run --out json=results.json script.js

# Artillery команды
artillery run config.yml
artillery run --output results.json config.yml
artillery report results.json

# Apache Bench
ab -n 1000 -c 10 https://example.com/
ab -n 1000 -c 10 -p data.json -T application/json https://api.example.com/users

# wrk
wrk -t12 -c400 -d30s https://example.com/
wrk -t12 -c400 -d30s -s script.lua https://api.example.com/

# Siege
siege -c 100 -t 5m https://example.com/
siege -c 100 -t 5m -f urls.txt

# Мониторинг системных ресурсов
htop
iotop
nethogs
glances

# Анализ логов
tail -f application.log | grep "ERROR"
grep "slow query" database.log | head -20
```
