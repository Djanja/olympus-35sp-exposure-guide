# Заметки по проекту

## Что делаем
Переводим дизайн-прототип (Claude Design / DC-фреймворк) в standalone `index.html` для GitHub Pages.
Один файл, Google Fonts CDN, всё остальное inline.

## Источники
- `project/Exposure Guide.dc.html` — React/DC прототип, основной визуальный референс
- `project/Style Comparison.dc.html` — сравнение двух скинов (camera + dark)
- `project/uploads/` — скриншоты из прототипа
- ТЗ — прислано пользователем в чате (детальная спецификация)

## Решения (согласовано с пользователем)

### Иконки
SVG из дизайна (не emoji из ТЗ):
- sun, partcloud, cloud, bulb, moon — погода в ячейках
- portrait, sharp, land, diff — характер кадра (ось диафрагм)
- safe, caution, blur — стабильность (ось выдержек)
- padlock — заблокированная ячейка

### Темы
Две: Camera (светлая) + Dark. Переключатель в шапке.

CSS переменные (из прототипа):
```
[data-skin="camera"]: --plate:#c3c6c8, --ink:#0d1012, --accent:#181a1c, --hl:#c97230
[data-skin="dark"]:   --plate:#111213, --ink:#ecedef, --accent:#c97230, --hl:#c97230
```

### EV формула
**Из ТЗ** (не из прототипа — в прототипе направление ISO перевёрнуто):
```js
EV = log2(f * f / t) + log2(iso / 100)
```

### Зоны EV (из ТЗ)
- lock:  EV < 5.5
- under: 5.5 ≤ EV ≤ 8
- work:  8 < EV ≤ 17
- over:  EV > 17

### Цвета зон
Динамический HSL из дизайна (меняется по зоне + погоде):
```js
const HUE = { sun:44, partcloud:78, cloud:192, bulb:28, moon:228 }
// camera: S = {lock:16, under:32, work:56, over:78}, L = {lock:72, under:70, work:66, over:60}
// dark:   S = {lock:18, under:28, work:42, over:60}, L = {lock:14, under:20, work:26, over:22}
```

### Погода (из дизайна, 5 категорий)
```js
if (ev >= 15) return 'sun'
if (ev >= 12) return 'partcloud'
if (ev >= 9)  return 'cloud'
if (ev >= 6)  return 'bulb'
else          return 'moon'
```

### Программная кривая
**Хардкод из ТЗ** (реальное поведение программного режима камеры):
```js
const curve100 = [
  {ti:3,fi:0}, {ti:4,fi:0}, {ti:5,fi:0}, {ti:5,fi:1},
  {ti:6,fi:1}, {ti:6,fi:2}, {ti:7,fi:2}, {ti:7,fi:3},
  {ti:8,fi:3}, {ti:8,fi:4}, {ti:8,fi:5}, {ti:8,fi:6},
]
// Сдвиг при смене ISO: shift = round(log2(iso/100)), ti_new = clamp(ti+shift, 0, 9)
```
Стиль: белая тень + оранжевая пунктирная линия (#D85A30 в ТЗ / var(--hl) в дизайне).
Метки: ○ на старте + ◆ на конце + текст "PROG" (как в дизайне).

### ГРИП
Данные из мануала (ТЗ), дистанции: 0.85, 1, 1.2, 1.5, 2, 3, 5, ∞ м.
Визуализация: числа + мини-бар в ячейке (как в прототипе).

### Тултип
Карточка-оверлей по центру экрана (как в дизайне, не bottom sheet).
Показывает: EV, зона, диафрагма, выдержка, погода.

### Легенда
Скрытая по умолчанию, кнопка внизу страницы.

### Мобильный
- Горизонтальный скролл таблицы
- Sticky ISO-бар
- SVG кривая перерисовывается на resize

## Шрифты (Google Fonts CDN)
- Bebas Neue — заголовок камеры
- Josefin Sans — UI, метки
- Courier Prime — числа в ячейках
- Cinzel — заголовок ГРИП

## Файлы
- `index.html` — целевой файл (создаём в корне)
- `NOTES.md` — этот файл
