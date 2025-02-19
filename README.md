[![made-with-markdown](https://img.shields.io/badge/Made%20with-Markdown-1f425f.svg)](https://daringfireball.net/projects/markdown/)
[![Chat](https://img.shields.io/badge/chat-t.me%2Fscrapy__python-blue.svg)](https://t.me/scrapy_python)
[![GitHub stars](https://img.shields.io/github/stars/bulatbulat48/ru-scrapy-python.svg?style=social&label=Star&maxAge=2592000)](https://GitHub.com/bulatbulat48/ru-scrapy-python/stargazers/)
[![GitHub contributors](https://img.shields.io/github/contributors/bulatbulat48/ru-scrapy-python.svg)](https://GitHub.com/bulatbulat48/ru-scrapy-python/graphs/contributors/)

# [@scrapy_python](https://t.me/scrapy_python) FAQ #

В этом репозитории находится полезная информация, собранная участниками чата.

### С чего начать? ###

* [Прочитать документацию](https://docs.scrapy.org/en/latest/)
* Базовые вопросы по питону [@ru_python_beginners](https://t.me/ru_python_beginners)

### Как ограничить количество реквестов? ###

* CLOSESPIDER_PAGECOUNT = 10

### Как спарсить JS? ###

* ставится Splash(удобно в Docker) и плагин [scrapy_splash](https://github.com/scrapy-plugins/scrapy-splash)
* смотреть откуда идут данные в Chrome -> devtools -> network -> XHR
* [JS to Python](http://piter.io/projects/js2py)

### Лучшие практики ###

* Использовать css селекторы чтобы избежать пробелов в названии при использовании @class в xpath, альтернатива "contains(@class, 'someclass')" выглядит сложнее.  
* Использовать xpath для поиска сложных значений, например в таблицах
* Использовать [inline-requests](https://github.com/rmax/scrapy-inline-requests) для синхронных запросов в функции
* Посмотреть мобильную версию

### Популярные css селекторы ###
* Достать href тега a: "a::attr(href)"
* Достать текст ноды: "title::text"
* Аналог contains у xpath, "a[href*=image] img::attr(src)"
* Можно добавить xpath ".css('img').xpath('@src')"
* [Проверка css-селекторов](https://www.w3schools.com/cssref/trysel.asp)
* [Список css-селекторов](https://www.w3schools.com/cssref/css_selectors.asp)

### Полезные библиотеки ###
* [html_text](https://github.com/TeamHG-Memex/html-text) - извлечь текст из сложного селектора, аналог .get_text(' ', strip=True) из BeautifulSoup, но быстрее и точнее.

### Полезные браузерные расширения ###
* [Selector Gadget](https://selectorgadget.com/) получить короткий css или xpath элемента(ов), см. видео на их сайте. Получается намного лучше встроенного в браузер copy as css/xpath.

### Нельзя мешать yield и return? ###
После return жизни нет. Нужно возвращать список или что-то итерируемое.

### Как вытащить узел по тексту внутри него используя css-селектор ###
Через CSS - никак. Использовать xpath contains. [Документация по xpath](http://www.zvon.org/comp/r/tut-XPath_1.html).

### Как поставить на windows ###
Простой способ - поставить в anaconda

### Как достать items из последнего job-а в scrapinghub? ###
https://app.scrapinghub.com/api/items.json?project=PROJECT&spider=SPIDERNAME&apikey=KEY
там где SPIDERNAME нужно вставить именно название, а не номер паука.
дополнительно можно почитать [тут](https://support.scrapinghub.com/support/solutions/articles/22000200409-fetching-latest-spider-data)

### Как спарсить данные из нескольких форм с POST-запросами ###
Использовать цикл по форме c FormRequest.from_response, дополнительное поле со счетчиком формы formnumber=counter и с фильтром dont_filter=True.

### Как обойти Cloudflare? ###
Страница отдает 503 ошибку. На этой странице javascript собирает код в форму с рандомным урлом и тремя hidden полями. После отправки этой формы отдается 302 редирект на нужную страницу. 

### Как передавать cookies ###
Иногда нужно чтобы паучок прикидывался зарегистрированным пользователем, а процесс автоматической авторизации может быть слишком хитро реализован на странице. В этом случае передача заранее подсмотренных после авторизации пользователя в браузере cookies может помочь. Сделать это в лоб довольно-таки просто и быстро:
* В settings.py активируйте ваши DOWNLOADER_MIDDLEWARES, хотя может быть это и лишнее действие.
* В settings.py выставьте `COOKIES_ENABLED = True`, чтобы scrapy сохранял передаваемые ему страницой cookies.
* В middlewares.py в методе обработки запросов process_request вашего DownloaderMiddleware прописать что-то такое:
```python
def process_request(self, request, spider):
    request.cookies[cookiename] = value     # вставьте ваши значения
    return request
```
* `COOKIES_DEBUG = True` в settings.py может помочь увидеть, что же происходит.

### Где найти дефолтные настройки Scrapy? ###
[default_settings.py в офф.репо](https://github.com/scrapy/scrapy/blob/master/scrapy/settings/default_settings.py)

### Как проанализировать запрос/форму? ###
Chrome -> devtools -> network -> клик на страницу -> copy as curl. Далее гуглим ["curl to python"](https://curl.trillworks.com/), вставляем код и получаем распаршенный код в библиотеке requests

### Чем проанализировать пакеты сети или воспроизвести запрос/форму? ###
Fiddler или postman(он умеет сразу в питонкод конвертить). Мощнее и сложнее wireshark.

### Обработка [кодов состояния HTTP](https://ru.wikipedia.org/wiki/%D0%A1%D0%BF%D0%B8%D1%81%D0%BE%D0%BA_%D0%BA%D0%BE%D0%B4%D0%BE%D0%B2_%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F_HTTP) ###
[По умолчанию скрапи обрабатывает успешные ответы](https://docs.scrapy.org/en/latest/topics/spider-middleware.html#module-scrapy.spidermiddlewares.httperror), для обработки остальных ответов используйте `handle_httpstatus_list`, например:

```python
class MySpider(CrawlSpider):
    handle_httpstatus_list = [404]
```

### Деплой Scrapy ###
* Хостинг [Scrapinghub](https://scrapinghub.com/) по дефолту стоит задержка, нужно отключать в настройках AUTOTHROTTLE_ENABLED чекбокс False
* UI для Scrapy [ScrapydWeb](https://github.com/my8100/scrapydweb) 
* Управление [Scrapyd](https://github.com/scrapy/scrapyd)

## Тесты ###
* [Spidermon](https://github.com/scrapinghub/spidermon)

### На сколько Scrapy быстрый? ###
Проверка N страниц.
* requests в один поток - бесконечное время
* scrapy из локальной машины - 30 минут
* scrapinghub с включенным по дефолту тротлингом - больше 1 часа
* scrapinghub без троттлинга 1 юнит - 23 минуты
* scrapinghub без троттлинга 3 юнита - 15 минут

### Можно ли использовать регулярные выражения в xpath? ###
Да, [можно](https://docs.scrapy.org/en/latest/topics/selectors.html#using-exslt-extensions)

### Мне нужно использовать регулярки, но я их боюсь. С чего начать? ###
* Посмотреть два туториала от Corey Shaffer: [How to Match Any Pattern of Text](https://www.youtube.com/watch?v=sa-TUpSx1JA) и [How to Write and Match Regular Expressions](https://www.youtube.com/watch?v=K8L6KVGG-7o)
* Практиковаться в любом редакторе, который поддерживает поиск по регам (например ST3 или Atom) - это не сложно
* Найти и прочитать [Mastering Python Regular Expressions](https://www.amazon.com/Mastering-Python-Regular-Expressions-Felix/dp/1783283157/)
* Вдумчиво прочитать [Тираногайд](https://www.rexegg.com/) по регуляркам

### Полезные ресурсы по Xpath ###
Справочники и туториалы с примерами:
* [Отличный гайд для начинающих от Guru99](https://www.guru99.com/xpath-selenium.html)
* [Интро от W3Schools](https://www.w3schools.com/xml/xpath_intro.asp)
* [Справочник-гайд от Javatpoint](https://www.javatpoint.com/xpath-tutorial)
* [Туториал по работе с xpath и xslt в библитеке lxml](https://lxml.de/xpathxslt.html)
* [от Zvon](http://zvon.org/comp/r/tut-XPath_1.html)
* [от TutorialsPoint](https://www.tutorialspoint.com/xpath/)

Подборка cheatsheets и bestpractices по xpath 
* [Подборка cheatsheets](http://scraping.pro/5-best-xpath-cheat-sheets-and-quick-references/)
* [Cheatsheet с примерами](https://devhints.io/xpath)
* [Лучшие практики](https://hackernoon.com/xpath-tips-from-the-web-scraping-trenches-fda06b0bf0a8)
* [Тренажер XPATH](https://www.freeformatter.com/xpath-tester.html)
