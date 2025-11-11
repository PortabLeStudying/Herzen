Вот пошаговая инструкция для выполнения задачи **с нуля**, согласно требованиям.

---

## 🧱 1. Создание нового проекта с Vite

Откройте терминал и выполните:

```bash
npm create vite@latest my-bootstrap-vite-app -- --template vanilla
cd my-bootstrap-vite-app
```

> Можно использовать `yarn`, `pnpm`, `bun` — подберите под вашу систему. Убедитесь, что Node.js ≥ 20.19 установлен.

Установите зависимости:

```bash
npm install
```

---

## 🧰 2. Установка необходимых зависимостей

Установим Bootstrap (только CSS, без JS компонентов), Luxon и Popper.js (нужен для модального окна Bootstrap):

```bash
npm install luxon
npm install bootstrap @popperjs/core
```

> Мы **не будем импортировать весь Bootstrap JS**, чтобы уменьшить бандл.

---

## 📁 3. Структура проекта

Ваша структура должна выглядеть так:

```
my-bootstrap-vite-app/
├── index.html
├── main.js
├── style.css (опционально)
├── package.json
└── vite.config.js (опционально)
```

---

## 📄 4. Настройка `index.html`

Замените содержимое `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Bootstrap + Vite + Luxon</title>
</head>
<body>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-6">
        <h2>Текущее время</h2>
        <p id="current-time"></p>
        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
          Показать модальное окно
        </button>
      </div>
    </div>
  </div>

  <!-- Модальное окно -->
  <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">Модальное окно</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          Это модальное окно из Bootstrap.
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Закрыть</button>
        </div>
      </div>
    </div>
  </div>

  <script type="module" src="/main.js"></script>
</body>
</html>
```

---

## 📜 5. Настройка `main.js`

Замените `main.js` на следующее:

```js
// CSS
import 'bootstrap/dist/css/bootstrap.min.css';

// JS только нужных компонентов
import { createPopper } from '@popperjs/core';
import { Modal } from 'bootstrap';

// Luxon
import { DateTime } from 'luxon';

// Обновление времени
function updateTime() {
  const now = DateTime.now().setLocale('ru').toLocaleString(DateTime.DATETIME_FULL);
  document.getElementById('current-time').textContent = now;
}

updateTime();
setInterval(updateTime, 1000);

// Инициализация модального окна
const modalElement = document.getElementById('exampleModal');
new Modal(modalElement);
```

> Мы импортируем **только `Modal`** из Bootstrap JS и **`createPopper`** — это минимизирует бандл.

---

## 🛠 6. Добавление команд сборки в `package.json`

Убедитесь, что в `package.json` есть:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

Команда для сборки проекта:

```bash
npm run build
```

Результат будет в папке `dist/`.

---

## 📏 7. Сборка и измерение размера бандла

1. Выполните сборку:

   ```bash
   npm run build
   ```

2. Посмотрите размер файлов в `dist/`:
   - `assets/index-*.js`
   - `assets/index-*.css`

3. **Сравнение**:  
   Для сравнения создайте **вторую сборку**, где импортируете **весь Bootstrap JS**:

   ```js
   import 'bootstrap';
   ```

   Пересоберите проект и сравните размеры бандлов.

> 🔍 Подсказка: используйте `ls -lh dist/assets/` или VS Code File Explorer.

Сделайте скриншоты обеих сборок → в отчет.

---

## 🧪 8. (Доп.) Минимизация и обфускация кода

Для минимизации используем плагин [`vite-plugin-html`](https://www.npmjs.com/package/vite-plugin-html) и встроенные возможности Vite (Rollup уже минимизирует JS/CSS).

Для обфускации JS → [`rollup-plugin-obfuscator`](https://www.npmjs.com/package/rollup-plugin-obfuscator) или [`terser`](https://terser.org/).

### Установка:

```bash
npm install -D vite-plugin-html terser
```

### Настройка `vite.config.js`:

```js
import { defineConfig } from 'vite';
import VitePluginHtml from 'vite-plugin-html';

export default defineConfig({
  plugins: [
    VitePluginHtml({
      minify: true,
    }),
  ],
  build: {
    minify: 'terser',
    terserOptions: {
      keep_classnames: false,
      keep_fnames: false,
    },
  },
});
```

Соберите с обфускацией:

```bash
npm run build
```

Сравните размер `dist/assets/index-*.js` **до и после**. Сделайте скриншот в DevTools → Network → размер JS-файла.

---

## 📤 9. Деплой

Деплой можно выполнить на:
- GitHub Pages
- Vercel
- Netlify

Например, для GitHub Pages:

```bash
npm install -D gh-pages
```

Добавьте в `package.json`:

```json
"deploy": "gh-pages -d dist"
```

И выполните:

```bash
npm run build
npm run deploy
```

---

## 📝 10. Отчет

В отчете укажите:

1. Ссылку на репозиторий (GitHub).
2. Скриншот UI (в браузере).
3. Ссылку на опубликованную страницу.
4. Сравнение размеров:
   - Минимальный бандл (только Modal + Luxon)
   - Полный Bootstrap
5. (Доп.) Сравнение с обфускацией.

---

## ✅ Итог

Теперь у вас есть:

- Проект на Vite с Bootstrap и Luxon
- Минимальный бандл благодаря селективному импорту
- Возможность сборки и деплоя
- Анализ размера и оптимизация

Если нужно — могу сгенерировать шаблон репозитория или пример `vite.config.js`.
