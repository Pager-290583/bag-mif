# Инструкция по настройке интеграции с Google Таблицами

## Шаг 1: Создание Google Таблицы

1. Откройте [Google Sheets](https://sheets.google.com)
2. Создайте новую таблицу
3. Назовите её, например, "Заявки от пользователей"
4. В первой строке создайте заголовки столбцов:
   - **A1**: Timestamp (Время)
   - **B1**: Type (Тип заявки)
   - **C1**: Name (Имя)
   - **D1**: Email
   - **E1**: Department (Отдел)
   - **F1**: Subject (Тема)
   - **G1**: Description (Описание)
   - **H1**: Priority (Приоритет)

## Шаг 2: Создание Google Apps Script

1. В вашей Google Таблице нажмите **Расширения** → **Apps Script**
2. Удалите весь существующий код и вставьте следующий:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  // Получаем данные из запроса
  var data = JSON.parse(e.postData.contents);
  
  // Добавляем новую строку с данными
  sheet.appendRow([
    data.timestamp || new Date().toISOString(),
    data.requestType || '',
    data.name || '',
    data.email || '',
    data.department || '',
    data.subject || '',
    data.description || '',
    data.priority || ''
  ]);
  
  // Возвращаем успешный ответ
  return ContentService.createTextOutput(JSON.stringify({
    'result': 'success',
    'message': 'Данные успешно сохранены'
  })).setMimeType(ContentService.MimeType.JSON);
}

function doGet(e) {
  return ContentService.createTextOutput(JSON.stringify({
    'status': 'OK',
    'message': 'Скрипт работает корректно'
  })).setMimeType(ContentService.MimeType.JSON);
}
```

3. Нажмите **Файл** → **Сохранить** (или Ctrl+S)
4. Назовите проект, например, "Обработчик заявок"

## Шаг 3: Развертывание скрипта

1. Нажмите кнопку **Развернуть** (Deploy) → **Новое развертывание**
2. Выберите тип: **Веб-приложение**
3. Заполните поля:
   - **Описание**: Версия 1
   - **Выполнять как**: Я (ваш email)
   - **У кого есть доступ**: **Все** (Anyone)
4. Нажмите **Развернуть**
5. Скопируйте **URL веб-приложения** (он выглядит как `https://script.google.com/macros/s/.../exec`)

## Шаг 4: Настройка сайта

1. Откройте файл `index.html`
2. Найдите строку:
   ```javascript
   const GOOGLE_SCRIPTS_URL = 'YOUR_GOOGLE_APPS_SCRIPT_URL';
   ```
3. Замените `YOUR_GOOGLE_APPS_SCRIPT_URL` на скопированный URL из шага 3
4. Сохраните файл

## Шаг 5: Проверка работы

1. Откройте `index.html` в браузере
2. Нажмите любую из трёх кнопок (Баги, Задачи, Электронная библиотека)
3. Заполните форму и нажмите "Отправить заявку"
4. Проверьте вашу Google Таблицу - данные должны появиться там

## Важные замечания

⚠️ **Безопасность**: 
- URL скрипта будет общедоступным (любой, кто знает URL, может отправлять данные)
- Для продакшена рекомендуется добавить дополнительную валидацию

⚠️ **CORS**: 
- Скрипт использует режим `no-cors`, что означает, что вы не получите ответ от сервера в браузере
- Данные всё равно будут сохраняться в таблице

⚠️ **Лимиты Google Apps Script**:
- Бесплатный аккаунт: до 100 запросов в день
- Google Workspace: до 6 минут выполнения в день

## Альтернативные варианты

Если вам нужна более сложная интеграция, рассмотрите:
1. **Google Forms API** - для создания форм напрямую в Google
2. **Zapier/Make** - для автоматизации между сервисами
3. **Backend-сервер** - Node.js/Python для обработки данных перед отправкой в Google
