# Signum Video - Website - API

Основные действия выполняются путем отправки запросов
на `https://c.signum.video`. Чаще всего API дает лишь **ссылку** (или **ключи**) на
запрашиваемый контент, и редко возвращает сам контент.

Ниже можно найти примеры кода для получения определенных ресурсов.
- [Signum Video - Website - API](#signum-video---website---api)
  - [Запуск прямой трансляции](#запуск-прямой-трансляции)
  - [Получение изображения с камер для превью](#получение-изображения-с-камер-для-превью)

## Запуск прямой трансляции
Для начала необходимо выполнить запрос `/api/live` на сайт с указанием всех необходимых
параметров.

## Получение изображения с камер для превью
Предположим, у нас имеется список с камерами:
```js
const cameras = [
  { cameraId: 1 },
  { cameraId: 2 },
  { cameraId: 3 },
]
```
Нам необходимо получить с них картинку для превью.
Происходить это будет в несколько этапов:
1) Отправка `GET /api/snapshot` запроса с параметров `cid` (ID камеры)
2) Отправка запроса на полученную ссылку
3) Конвертация полученных данных в `Blob`
4) Получение ссылки с помощью `URL.createObjectURL`

Простой пример кода для получения превью камер может выглядить следующим образом:
```js
const cameras = [
  { cameraId: 1 },
  { cameraId: 2 },
  { cameraId: 3 },
]

// 1. Формируем ссылку и отправляем запрос на сайт
const params = new URLSearchParams()
for (let c of cameras)
  params.append('cid', c.cameraId)

let response = await fetch('/api/snapshot?' + params.toString())
// {"url": "https://x.c.signum.video/p/.."}

// 2. Получаем от сайта ссылку и выполняем на нее запрос
const json = await response.json()
response = await fetch(json.url)
// [
//   {
//     "cameraId": 1,
//     "data": <String>,
//     "mime": <String> 
//   },
//   {
//     "cameraId": 2,
//     "data": <String>,
//     "mime": <String> 
//   },
//   ...
// ]


function dataToBlob(data, mime) {
  const byteCharacters = atob(data)
  const byteNumbers = new Array(byteCharacters.length)
  for (let i = 0; i < byteCharacters.length; i++)
      byteNumbers[i] = byteCharacters.charCodeAt(i)
  const byteArray = new Uint8Array(byteNumbers)
  return new Blob([byteArray], {type: mimeType})
}

for (let camera of await response.json()) {
  // 3. Конвертируем в Blob
  const b = dataToBlob(camera.data, camera.mime)
  // 4. Получаем ссылку на Blob
  const imageUrl = URL.createObjectURL(b) 
}
```