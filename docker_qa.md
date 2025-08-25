# 🐳 Docker - Вопросы и Ответы

## Основы Docker

**Q: Что такое Docker и зачем он нужен?**
A: Docker - это платформа для контейнеризации приложений. Позволяет упаковать приложение со всеми зависимостями в "контейнер", который работает одинаково на любой системе.

**Простая аналогия:**
- **Обычное приложение** = как рецепт блюда (может не получиться на другой кухне)
- **Docker контейнер** = как готовое блюдо в контейнере (вкусно везде)

**Преимущества:**
- ✅ Одинаково работает везде
- ✅ Быстрое развертывание
- ✅ Изоляция приложений
- ✅ Эффективное использование ресурсов

**Q: В чем разница между образом и контейнером?**
A:

| Образ (Image) | Контейнер (Container) |
|---------------|----------------------|
| **Что это** | Шаблон/шаблон | Работающая копия |
| **Аналогия** | Чертеж дома | Построенный дом |
| **Изменения** | Неизменяемый | Можно изменять |
| **Количество** | Один образ | Много контейнеров |
| **Пример** | `nginx:latest` | `my-nginx-container` |

**Простая аналогия:**
- **Образ** = как установочный файл программы
- **Контейнер** = как запущенная программа

## Работа с образами

**Q: Как работать с Docker образами?**
A:

```bash
# Посмотреть все образы
docker images
docker image ls

# Скачать образ из реестра
docker pull nginx:latest
docker pull ubuntu:20.04

# Создать образ из Dockerfile
docker build -t my-app:v1 .

# Посмотреть образы с фильтром
docker images | grep nginx

# Удалить образ
docker rmi nginx:latest
docker image rm nginx:latest

# Удалить все неиспользуемые образы
docker image prune -a

# Посмотреть историю образа
docker history nginx:latest

# Сохранить образ в файл
docker save -o nginx.tar nginx:latest

# Загрузить образ из файла
docker load -i nginx.tar

# Посмотреть детальную информацию
docker inspect nginx:latest
```

**Q: Как создать свой образ?**
A: Создайте файл `Dockerfile`:

```dockerfile
# Базовый образ
FROM ubuntu:20.04

# Установка пакетов
RUN apt-get update && apt-get install -y nginx

# Копирование файлов
COPY index.html /var/www/html/

# Открытие порта
EXPOSE 80

# Команда запуска
CMD ["nginx", "-g", "daemon off;"]
```

**Сборка образа:**
```bash
docker build -t my-nginx:v1 .
```

## Работа с контейнерами

**Q: Как управлять контейнерами?**
A:

```bash
# Запустить контейнер
docker run -d --name my-nginx nginx:latest
docker run -d -p 8080:80 --name web nginx:latest

# Посмотреть запущенные контейнеры
docker ps

# Посмотреть все контейнеры (включая остановленные)
docker ps -a

# Остановить контейнер
docker stop my-nginx

# Запустить остановленный контейнер
docker start my-nginx

# Перезапустить контейнер
docker restart my-nginx

# Удалить контейнер
docker rm my-nginx

# Удалить все остановленные контейнеры
docker container prune

# Войти в контейнер
docker exec -it my-nginx bash
docker exec -it my-nginx sh

# Посмотреть логи контейнера
docker logs my-nginx
docker logs -f my-nginx  # следить за логами
```

**Q: Как настроить сеть для контейнеров?**
A:

```bash
# Создать сеть
docker network create my-network

# Запустить контейнер в сети
docker run -d --name web --network my-network nginx:latest

# Посмотреть сети
docker network ls

# Посмотреть детали сети
docker network inspect my-network

# Подключить контейнер к сети
docker network connect my-network existing-container

# Отключить от сети
docker network disconnect my-network container-name
```

**Q: Как работать с томами (volumes)?**
A:

```bash
# Создать том
docker volume create my-data

# Запустить контейнер с томом
docker run -d --name db -v my-data:/var/lib/mysql mysql:latest

# Монтировать папку хоста
docker run -d --name web -v /home/user/app:/var/www/html nginx:latest

# Посмотреть тома
docker volume ls

# Посмотреть информацию о томе
docker volume inspect my-data

# Удалить том
docker volume rm my-data
```

## Docker Compose

**Q: Что такое Docker Compose и зачем он нужен?**
A: Docker Compose - это инструмент для управления многоконтейнерными приложениями. Позволяет описать всю инфраструктуру в одном файле.

**Преимущества:**
- ✅ Один файл для всего приложения
- ✅ Простое управление зависимостями
- ✅ Легкое развертывание
- ✅ Воспроизводимая среда

**Q: Как создать docker-compose.yml?**
A:

```yaml
version: '3.8'

services:
  # Веб-сервер
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/var/www/html
    depends_on:
      - db
    networks:
      - app-network

  # База данных
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: myapp
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - app-network

  # Приложение
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=db
      - DB_USER=root
      - DB_PASSWORD=secret
    depends_on:
      - db
    networks:
      - app-network

volumes:
  db-data:

networks:
  app-network:
    driver: bridge
```

**Q: Как управлять приложением через Compose?**
A:

```bash
# Запустить все сервисы
docker-compose up -d

# Остановить все сервисы
docker-compose down

# Посмотреть статус сервисов
docker-compose ps

# Посмотреть логи
docker-compose logs
docker-compose logs web
docker-compose logs -f web

# Пересобрать и запустить
docker-compose up --build

# Запустить только один сервис
docker-compose up -d web

# Выполнить команду в сервисе
docker-compose exec db mysql -u root -p

# Масштабировать сервис
docker-compose up -d --scale web=3

# Остановить и удалить все (включая тома)
docker-compose down -v
```

**Q: Как использовать переменные окружения в Compose?**
A:

**Создайте файл `.env`:**
```env
DB_PASSWORD=mysecretpassword
DB_NAME=myapp
WEB_PORT=8080
```

**В docker-compose.yml:**
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "${WEB_PORT}:80"
    environment:
      - DB_PASSWORD=${DB_PASSWORD}
      - DB_NAME=${DB_NAME}
```

## Docker Swarm

**Q: Что такое Docker Swarm и зачем он нужен?**
A: Docker Swarm - это встроенная система оркестрации контейнеров. Позволяет управлять кластером Docker-хостов как единой системой.

**Преимущества:**
- ✅ Автоматическое распределение нагрузки
- ✅ Высокая доступность
- ✅ Автоматическое восстановление
- ✅ Масштабирование

**Q: Как настроить Swarm кластер?**
A:

```bash
# Инициализировать Swarm на manager-ноде
docker swarm init --advertise-addr 192.168.1.100

# Получить токен для присоединения worker-нод
docker swarm join-token worker

# Присоединить worker-ноду к кластеру
docker swarm join --token SWMTKN-1-xxx 192.168.1.100:2377

# Посмотреть ноды в кластере
docker node ls

# Посмотреть информацию о ноде
docker node inspect node-name

# Продвинуть worker до manager
docker node promote worker-node

# Понизить manager до worker
docker node demote manager-node
```

**Q: Как развернуть сервис в Swarm?**
A:

```bash
# Создать сеть
docker network create --driver overlay my-network

# Развернуть сервис
docker service create --name web --replicas 3 -p 80:80 nginx:latest

# Посмотреть сервисы
docker service ls

# Посмотреть детали сервиса
docker service ps web

# Масштабировать сервис
docker service scale web=5

# Обновить сервис
docker service update --image nginx:1.19 web

# Удалить сервис
docker service rm web
```

**Q: Как использовать docker-compose с Swarm?**
A:

**Создайте docker-compose.yml:**
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
    networks:
      - web

networks:
  web:
    external: true
```

**Развертывание:**
```bash
# Создать сеть
docker network create --driver overlay web

# Развернуть стек
docker stack deploy -c docker-compose.yml myapp

# Посмотреть стеки
docker stack ls

# Посмотреть сервисы в стеке
docker stack services myapp

# Удалить стек
docker stack rm myapp
```

## Мониторинг и диагностика

**Q: Как мониторить Docker?**
A:

```bash
# Статистика контейнеров
docker stats
docker stats --no-stream

# Статистика системы
docker system df

# Использование диска
docker system prune -a

# Информация о контейнере
docker inspect container-name

# Процессы в контейнере
docker top container-name

# Изменения в контейнере
docker diff container-name

# Экспорт контейнера
docker export container-name > container.tar

# Импорт контейнера
docker import container.tar new-image:tag
```

**Q: Как диагностировать проблемы?**
A:

```bash
# Проверить события Docker
docker events

# Посмотреть информацию о системе
docker info

# Проверить версию Docker
docker version

# Проверить сетевые настройки
docker network inspect bridge

# Проверить тома
docker volume inspect volume-name

# Посмотреть логи демона Docker
journalctl -u docker.service -f
```

## Полезные команды

**Q: Какие самые полезные команды Docker?**
A:

```bash
# Очистка системы
docker system prune -a --volumes

# Посмотреть все образы с размерами
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Посмотреть контейнеры с размерами
docker ps -s

# Копировать файлы в/из контейнера
docker cp local-file.txt container:/path/
docker cp container:/path/file.txt ./

# Создать образ из контейнера
docker commit container-name new-image:tag

# Сохранить контейнер
docker export container-name | gzip > container.tar.gz

# Загрузить контейнер
gunzip -c container.tar.gz | docker import - new-image:tag

# Посмотреть слои образа
docker history --no-trunc image-name

# Проверить конфигурацию Dockerfile
docker build --dry-run .
```

**Q: Как работать с Docker Registry?**
A:

```bash
# Войти в registry
docker login registry.example.com

# Пометить образ для registry
docker tag my-image:latest registry.example.com/my-image:latest

# Отправить образ в registry
docker push registry.example.com/my-image:latest

# Скачать образ из registry
docker pull registry.example.com/my-image:latest

# Выйти из registry
docker logout registry.example.com
```

## Лучшие практики

**Q: Какие лучшие практики работы с Docker?**
A:

**1. Оптимизация образов:**
- Используйте многоэтапную сборку
- Минимизируйте количество слоев
- Удаляйте ненужные файлы
- Используйте .dockerignore

**2. Безопасность:**
- Не запускайте контейнеры от root
- Используйте официальные образы
- Регулярно обновляйте образы
- Сканируйте образы на уязвимости

**3. Мониторинг:**
- Настройте логирование
- Мониторьте использование ресурсов
- Используйте health checks
- Настройте алерты

**4. Организация:**
- Используйте теги для версионирования
- Документируйте Dockerfile
- Используйте docker-compose для сложных приложений
- Настройте CI/CD для автоматической сборки
