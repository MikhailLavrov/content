## Просмотр HTTP-сообщений из командной строки

В мире Unix-подобных операционных систем есть многофункциональная утилита _curl_, которая позволяет работать с протоколом HTTP. С одной стороны, с её помощью можно скачивать файлы, отправлять данные пользователей на сервер аналогично тому, как это делается через формы на сайтах, работать с API и даже отправлять письмо. С другой, это отличный способ познакомиться с работой протокола или протестировать веб-приложение, которое работает на стороне сервера.

Чтобы скачать главную страницу какого-то сайта (например, _http://example.com_), можно воспользоваться командой:

```bash
curl http://example.com
```

В терминале вы увидите HTML-код страницы — это тело сообщения ответа. Существует возможность посмотреть и полные сообщения запроса и ответа, добавив ключ `-v`:

```bash
curl -v http://example.com
*   Trying 93.184.216.34...
* TCP_NODELAY set
* Connected to example.com (93.184.216.34) port 80 (#0)
> GET / HTTP/1.1
> Host: example.com
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Age: 258083
< Cache-Control: max-age=604800
< Content-Type: text/html; charset=UTF-8
< Date: Thu, 22 Jul 2021 15:42:57 GMT
< Etag: "3147526947+ident"
< Expires: Thu, 29 Jul 2021 15:42:57 GMT
< Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
< Server: ECS (dcb/7F17)
< Vary: Accept-Encoding
< X-Cache: HIT
< Content-Length: 1256
<
<!doctype html>
<html>

* Connection #0 to host example.com left intact
* Closing connection 0
```

В выводе команды curl символы `*`, `>` и `<` являются маркерами типа информации, отображённой в выводе команды. `*` используется для сервисной информации о соединении, `>` — для обозначения запросов клиента (клиентом является curl), `<` — для обозначения ответов сервера.

В первой части вывода содержится информация о процессе установления соединения через протокол TCP:

```bash
*   Trying 93.184.216.34...
* TCP_NODELAY set
* Connected to example.com (93.184.216.34) port 80 (#0)
```

Curl пытается соединиться с сетевым устройством с IP-адресом _93.184.216.34_, поскольку именно этот адрес соответствует домену _example.com_. Строка после слова `Connected...` curl указывает, что соединение установлено. В последней части curl выводит информацию о прекращении соединения:

```bash
* Connection #0 to host example.com left intact
* Closing connection 0
```

Вторая часть вывода — полное HTML-сообщение запроса со стартовой строкой и заголовками (тело сообщения отсутствует):

- `> GET / HTTP/1.1` — Стартовая строка с методом запроса, адресом внутри сайта и версией протокола.
- `> Host: example.com` — Заголовок с указанием хоста, на котором работает веб-сервер.
- `> User-Agent: curl/7.64.1` — Заголовок с информацией о клиенте, программе, запущенной у пользователя.
- `> Accept: */*` — Заголовок с типами контента, которые клиент может корректно обработать.
- `> ` — После этой пустой строки могло быть тело запроса, но его редко используют в случае обработки данных методом GET.

Третья часть вывода — полное HTML-сообщение ответа веб-сервера со стартовой строкой, заголовками и телом, в котором передаётся HTML-код страницы:

`< HTTP/1.1 200 OK` — Стартовая строка с версией протокола, кодом и статусом ответа сервера.
`< Age: 258083` — Заголовок с временем жизни контента в кэше (в секундах).
`< Cache-Control: max-age=604800` — Заголовок для определения времени (в секундах) и типа кэширования на сервере.
`< Content-Type: text/html; charset=UTF-8` — Заголовок с типом контента и кодировкой.
`< Date: Thu, 22 Jul 2021 15:42:57 GMT` — Заголовок с датой и временем отправки сообщения.
`< Etag: "3147526947+ident"` — Заголовок с версией контента.
`< Expires: Thu, 29 Jul 2021 15:42:57 GMT` — Заголовок с датой и временем, после которых контент считается устаревшим.
`< Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT` — Заголовок с датой  и временем последнего изменения контента.
`< Server: ECS (dcb/7F17)` — Заголовок с именем веб-сервера. В нашем случае Elastic Cloud Server.
`< Vary: Accept-Encoding` — Заголовок, который отвечает за выбор стратегии кэширования и сжатия.
`< X-Cache: HIT` — Заголовок означает, что контент расположен на CDN (Content Delivery Network), а не на одном сервере.
`< Content-Length: 1256` — Заголовок с информацией о длине контента в символах.
`< ` — После этой пустой строки идёт тело ответа сервера, в нашем случае HTML-код страницы

Вы можете узнать больше о домене с помощью разных сервисов, например, с помощью [domain.glass](https://domain.glass/example.com).