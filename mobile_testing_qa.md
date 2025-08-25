# 📱 Мобильное тестирование - Вопросы и Ответы

## Основы мобильного тестирования

**Q: Что такое мобильное тестирование и чем оно отличается от веб-тестирования?**
A: Мобильное тестирование - это процесс проверки мобильных приложений на различных устройствах, операционных системах и сетевых условиях.

**Ключевые отличия от веб-тестирования:**
- ✅ Разнообразие устройств и экранов
- ✅ Различные операционные системы (iOS, Android)
- ✅ Специфичные жесты (свайп, пинч, тап)
- ✅ Работа с сенсорами (GPS, акселерометр, камера)
- ✅ Ограниченные ресурсы (память, батарея)
- ✅ Различные сетевые условия (Wi-Fi, 3G, 4G, 5G)

**Q: Какие типы мобильного тестирования существуют?**
A:

| Тип тестирования | Что проверяет | Примеры |
|------------------|---------------|---------|
| **Функциональное** | Основные функции приложения | Логин, регистрация, покупки |
| **UI/UX** | Интерфейс и пользовательский опыт | Навигация, отзывчивость |
| **Производительность** | Скорость и эффективность | Время загрузки, использование памяти |
| **Совместимость** | Работа на разных устройствах | Различные экраны, версии ОС |
| **Безопасность** | Защита данных и уязвимости | Шифрование, авторизация |
| **Сетевое** | Работа в разных сетевых условиях | Wi-Fi, мобильный интернет |

## Инструменты мобильного тестирования

**Q: Какие популярные инструменты для автоматизации мобильного тестирования?**
A:

### Appium
**Описание:** Кроссплатформенный инструмент для автоматизации мобильных приложений.

**Пример теста на Python:**
```python
from appium import webdriver
from appium.webdriver.common.mobileby import MobileBy

# Настройка capabilities
desired_caps = {
    'platformName': 'Android',
    'deviceName': 'Android Emulator',
    'app': '/path/to/app.apk',
    'automationName': 'UiAutomator2'
}

# Подключение к Appium серверу
driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)

# Тест логина
def test_login():
    # Найти поле email
    email_field = driver.find_element(MobileBy.ID, 'com.example.app:id/email')
    email_field.send_keys('user@example.com')
    
    # Найти поле пароля
    password_field = driver.find_element(MobileBy.ID, 'com.example.app:id/password')
    password_field.send_keys('password123')
    
    # Нажать кнопку входа
    login_button = driver.find_element(MobileBy.ID, 'com.example.app:id/login')
    login_button.click()
    
    # Проверить успешный вход
    welcome_text = driver.find_element(MobileBy.ID, 'com.example.app:id/welcome')
    assert welcome_text.text == 'Добро пожаловать!'

driver.quit()
```

### Detox (React Native)
**Описание:** Инструмент для автоматизации React Native приложений.

**Пример теста на JavaScript:**
```javascript
describe('Login Flow', () => {
  it('should login successfully', async () => {
    // Запуск приложения
    await device.launchApp();
    
    // Ввод email
    await element(by.id('email')).typeText('user@example.com');
    
    // Ввод пароля
    await element(by.id('password')).typeText('password123');
    
    // Нажатие кнопки входа
    await element(by.id('login-button')).tap();
    
    // Проверка успешного входа
    await expect(element(by.text('Добро пожаловать!'))).toBeVisible();
  });
  
  it('should show error for invalid credentials', async () => {
    await device.launchApp();
    
    await element(by.id('email')).typeText('invalid@example.com');
    await element(by.id('password')).typeText('wrongpassword');
    await element(by.id('login-button')).tap();
    
    // Проверка сообщения об ошибке
    await expect(element(by.text('Неверные учетные данные'))).toBeVisible();
  });
});
```

## Тестирование на реальных устройствах

**Q: Как организовать тестирование на реальных устройствах?**
A:

### Облачные платформы тестирования

**Firebase Test Lab:**
```bash
# Установка Firebase CLI
npm install -g firebase-tools

# Авторизация
firebase login

# Запуск тестов на реальных устройствах
gcloud firebase test android run \
  --type instrumentation \
  --app app-debug.apk \
  --test app-debug-test.apk \
  --device model=redfin,version=30,locale=en,orientation=portrait
```

**AWS Device Farm:**
```python
import boto3

# Создание клиента Device Farm
client = boto3.client('devicefarm')

# Загрузка приложения
with open('app.apk', 'rb') as f:
    app_upload = client.upload_application(
        projectArn='arn:aws:devicefarm:us-west-2:123456789012:project:example',
        name='MyApp',
        type='ANDROID_APP'
    )

# Запуск тестов
run = client.schedule_run(
    projectArn='arn:aws:devicefarm:us-west-2:123456789012:project:example',
    appArn=app_upload['upload']['arn'],
    devicePoolArn='arn:aws:devicefarm:us-west-2:123456789012:devicepool:example',
    test={
        'type': 'INSTRUMENTATION',
        'testPackageArn': test_package_arn
    }
)
```

## Тестирование жестов и сенсоров

**Q: Как тестировать жесты и сенсоры в мобильных приложениях?**
A:

### Тестирование жестов в Appium
```python
from appium.webdriver.common.touch_action import TouchAction

def test_swipe_gesture():
    # Найти элемент для свайпа
    element = driver.find_element(MobileBy.ID, 'com.example.app:id/swipeable')
    
    # Создать объект для жестов
    actions = TouchAction(driver)
    
    # Выполнить свайп влево
    actions.press(element).wait(1000).move_to(x=-200, y=0).release().perform()
    
    # Проверить результат свайпа
    result = driver.find_element(MobileBy.ID, 'com.example.app:id/result')
    assert result.text == 'Свайп выполнен'

def test_pinch_gesture():
    # Найти элемент для пинча
    element = driver.find_element(MobileBy.ID, 'com.example.app:id/image')
    
    # Координаты для пинча
    center_x = element.location['x'] + element.size['width'] // 2
    center_y = element.location['y'] + element.size['height'] // 2
    
    # Выполнить пинч (зум)
    actions = TouchAction(driver)
    actions.press(x=center_x-50, y=center_y-50).wait(1000)\
           .move_to(x=center_x-100, y=center_y-100).release()\
           .press(x=center_x+50, y=center_y+50).wait(1000)\
           .move_to(x=center_x+100, y=center_y+100).release()\
           .perform()
```

### Тестирование сенсоров
```python
def test_gps_location():
    # Установить GPS координаты
    driver.set_location(latitude=55.7558, longitude=37.6176, altitude=100)
    
    # Проверить отображение локации в приложении
    location_text = driver.find_element(MobileBy.ID, 'com.example.app:id/location')
    assert 'Москва' in location_text.text

def test_orientation_change():
    # Изменить ориентацию на ландшафт
    driver.orientation = 'LANDSCAPE'
    
    # Проверить адаптацию интерфейса
    landscape_element = driver.find_element(MobileBy.ID, 'com.example.app:id/landscape_view')
    assert landscape_element.is_displayed()
    
    # Вернуть портретную ориентацию
    driver.orientation = 'PORTRAIT'
```

## Тестирование производительности

**Q: Как тестировать производительность мобильных приложений?**
A:

### Использование Android Profiler
```bash
# Запуск профилирования через ADB
adb shell am start -n com.example.app/.MainActivity
adb shell am profile start com.example.app /sdcard/profile.trace

# Выполнение действий в приложении
# ...

# Остановка профилирования
adb shell am profile stop com.example.app

# Получение файла профиля
adb pull /sdcard/profile.trace ./profile.trace
```

### Тестирование памяти в Appium
```python
def test_memory_usage():
    # Получить информацию о памяти
    memory_info = driver.get_performance_data('com.example.app', 'memoryinfo', 5)
    
    # Проверить использование памяти
    for info in memory_info:
        memory_usage = int(info['totalPss'])
        assert memory_usage < 100000000  # Меньше 100MB
        
def test_battery_usage():
    # Получить информацию о батарее
    battery_info = driver.get_performance_data('com.example.app', 'batteryinfo', 5)
    
    # Проверить потребление батареи
    for info in battery_info:
        battery_usage = float(info['power'])
        assert battery_usage < 50.0  # Меньше 50% в час
```

## Тестирование сетевых условий

**Q: Как тестировать приложение в различных сетевых условиях?**
A:

### Симуляция сетевых условий
```python
def test_slow_network():
    # Симуляция медленного интернета
    driver.set_network_connection(1)  # Только Wi-Fi, медленно
    
    # Тест загрузки контента
    start_time = time.time()
    driver.find_element(MobileBy.ID, 'com.example.app:id/load_content').click()
    
    # Ждать загрузки
    WebDriverWait(driver, 30).until(
        EC.presence_of_element_located((MobileBy.ID, 'com.example.app:id/content'))
    )
    
    load_time = time.time() - start_time
    assert load_time < 30  # Загрузка должна завершиться за 30 секунд

def test_offline_mode():
    # Отключить сеть
    driver.set_network_connection(0)  # Нет сети
    
    # Проверить офлайн режим
    offline_message = driver.find_element(MobileBy.ID, 'com.example.app:id/offline_message')
    assert offline_message.text == 'Нет подключения к интернету'
    
    # Включить сеть обратно
    driver.set_network_connection(6)  # Все сети включены
```

## Лучшие практики

**Q: Какие лучшие практики мобильного тестирования?**
A:

### 1. Стратегия тестирования
```python
# Использование Page Object Model для мобильных приложений
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
    
    def enter_email(self, email):
        email_field = self.driver.find_element(MobileBy.ID, 'com.example.app:id/email')
        email_field.clear()
        email_field.send_keys(email)
    
    def enter_password(self, password):
        password_field = self.driver.find_element(MobileBy.ID, 'com.example.app:id/password')
        password_field.clear()
        password_field.send_keys(password)
    
    def tap_login(self):
        login_button = self.driver.find_element(MobileBy.ID, 'com.example.app:id/login')
        login_button.click()
    
    def is_logged_in(self):
        try:
            welcome_element = self.driver.find_element(MobileBy.ID, 'com.example.app:id/welcome')
            return welcome_element.is_displayed()
        except:
            return False

# Использование в тестах
def test_login_flow():
    login_page = LoginPage(driver)
    login_page.enter_email('user@example.com')
    login_page.enter_password('password123')
    login_page.tap_login()
    assert login_page.is_logged_in()
```

### 2. Обработка ошибок
```python
def test_network_error_handling():
    # Симуляция сетевой ошибки
    driver.set_network_connection(0)
    
    try:
        # Попытка выполнить действие, требующее сеть
        driver.find_element(MobileBy.ID, 'com.example.app:id/sync').click()
        
        # Проверить отображение ошибки
        error_message = WebDriverWait(driver, 10).until(
            EC.presence_of_element_located((MobileBy.ID, 'com.example.app:id/error'))
        )
        assert 'Сетевая ошибка' in error_message.text
        
    finally:
        # Восстановить сеть
        driver.set_network_connection(6)
```

### 3. Параллельное тестирование
```python
import pytest
from appium import webdriver

@pytest.fixture(params=[
    {'platformName': 'Android', 'deviceName': 'Pixel_4_API_30'},
    {'platformName': 'Android', 'deviceName': 'Samsung_Galaxy_S10'},
    {'platformName': 'iOS', 'deviceName': 'iPhone_12_Simulator'}
])
def mobile_driver(request):
    driver = webdriver.Remote('http://localhost:4723/wd/hub', request.param)
    yield driver
    driver.quit()

def test_cross_platform_login(mobile_driver):
    # Тест будет запущен на всех устройствах
    login_page = LoginPage(mobile_driver)
    login_page.enter_email('user@example.com')
    login_page.enter_password('password123')
    login_page.tap_login()
    assert login_page.is_logged_in()
```

## Полезные команды

**Q: Какие полезные команды для мобильного тестирования?**
A:

```bash
# Просмотр подключенных устройств
adb devices

# Установка приложения
adb install app.apk

# Удаление приложения
adb uninstall com.example.app

# Просмотр логов приложения
adb logcat | grep "com.example.app"

# Сделать скриншот
adb shell screencap /sdcard/screenshot.png
adb pull /sdcard/screenshot.png ./

# Запись видео экрана
adb shell screenrecord /sdcard/video.mp4
adb pull /sdcard/video.mp4 ./

# Просмотр информации о приложении
adb shell dumpsys package com.example.app

# Просмотр использования памяти
adb shell dumpsys meminfo com.example.app

# Просмотр информации о батарее
adb shell dumpsys battery

# Симуляция нажатия клавиш
adb shell input keyevent KEYCODE_HOME
adb shell input keyevent KEYCODE_BACK

# Симуляция тапа по координатам
adb shell input tap 500 500

# Симуляция свайпа
adb shell input swipe 500 500 100 500
```
