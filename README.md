# Тестирование на проникновение DVWA — отчёт и практика

Тестирование на проникновение учебного стенда **DVWA (Damn Vulnerable Web Application)** в локальной Docker-лаборатории. Скоуп: анализ защищённости веб-приложения, эксплуатация уязвимостей, оценка рисков по CVSS, подготовка отчётной документации.

## Стенд
- DVWA v1.10 (Development), уровень защиты security = low
- Локальный контейнер Docker `vulnerables/web-dvwa` на `http://localhost:8080`
- Методика оценки: OWASP Top 10 (2021), CVSS v3.1

## Найденные уязвимости

| № | Уязвимость | Тип | Риск (CVSS) | Модуль |
|---|------------|-----|-------------|--------|
| 1 | SQL Injection (UNION-based, дамп учётных записей) | Injection | 9.8 Critical | SQLi |
| 2 | Command Injection → RCE | Injection | 9.8 Critical | Exec |
| 3 | Reflected XSS | XSS | 6.1 Medium | XSS (Reflected) |
| 4 | Stored XSS | XSS | 8.2 High | XSS (Stored) |
| 5 | Local File Inclusion (`/etc/passwd`) | Injection | 7.5 High | File Inclusion |
| 6 | Unrestricted File Upload → RCE | Misconfiguration | 8.8 High | File Upload |

## Чем подтверждалось
- SQL-инъекция: извлечена вся таблица пользователей, включая пароль администратора (MD5 → расшифрован)
- Command Injection: выполнение `id`, `whoami`, `uname -a` от имени `www-data`
- Stored XSS: payload хранится и исполняется у всех посетителей гостевой книги
- LFI: чтение `/etc/passwd`
- File Upload: загрузка веб-шелла и выполнение произвольных команд (RCE) с последующей очисткой стенда

## Отчёт
Полный отчёт с описанием, эксплуатацией, рекомендациями: **`отчёт_dvwa_пентест.md`**.

## Воспроизведение стенда
```bash
docker run -d --name dvwa -p 8080:80 vulnerables/web-dvwa
```
- URL: http://localhost:8080
- Логин: `admin` / `password`
- Создать БД: вкладка Setup → Create / Reset Database
- Уровень: Security → low

> Все действия выполнены в изолированной лаборатории **только на собственном локальном стенде**. Никакие сторонние системы не затрагивались.