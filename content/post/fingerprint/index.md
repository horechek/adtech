+++
date = "2019-01-27"
title = "Fingerprint или цифровой отпечаток устройства"
categories = [ "Инструменты" ]
tags = [ "Id", "Fingerprints", "Browser" ]
draft = true

[image]
  # Caption (optional)
  caption = "Изображение: Pixel Privacy"
  
  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Smart"

  preview_only = false
+++

Fingerprint дословно переводится как отпечатки пальцев. Эти отпечатки используются для идентификации людей. Похожим образом используются цифровые отпечатки - идентифицируют браузеры и устройства для трекинга пользователей в социальных сетях, интернет рекламе, системах аналитики.

С помощью цифровых отпечатков отличают один браузер от другого или различают устройства. В первом случае у вас поменяется отпечаток если вы начнете использовать другой браузер, например фаерфокс вместо хрома. Во втором случае, даже если вы перейдете в режим инкогнито или откроете другой браузер, отпечаток не поменяется.

Идентификация пользователей это очень важная тема для рекламной индустрии. Вся персональная информация, профили, аудиторные таргетинги привязываются к некоторым уникальным идентификаторам. Чем надежнее эти идентификаторы, тем более подходящую рекламу видит пользователь.

## Как работают цифровые отпечатки

Браузерные отпечатки проще - можно использовать меньше параметров и не нужно учитывать различия между браузерами. Отпечатки устройства сложнее - сложно добраться до информации о операционной системе или устройстве.

Цифровые отпечатки это закодированная информация о различных параметрах, например о максимальном разрешение, установленных шрифта и плагинах. Полученная информация кешируется и результат кеширования используется как идентификатор пользователя.

Системы трекинга постоянно развиваются. Условно различают три поколения таких систем.

* Первое поколение. Идентификаторы генерируются на стороне сервера и сохраняются в cookies браузера.
* Второе поколение. Цифровые отпечатки браузеров. Для их генерации используется информация о разрешении экрана, установленных шрифтах, вся информация из юзер агента браузера, информация о видео и аудио кодеках, canvas и webgl рендеринг. Часто второе поколение комбинируется с первым. Идентификаторы генерируются с помощью JS на клиентской стороне, сохраняются в cookie и передаются на сервер. Для большей надежности, вместо сохранения в cookies используют evercookie - технология сохранения идентификаторов так, чтобы их было сложно удалить.
* Третье поколение. Цифровые отпечатки устройств. Они тоже генерируются с помощью JS на клиентской стороне. Но используется информация о операционной системе и устройстве, а не о браузере. Некоторые параметры, например webgl рендеринг или информация о кодеках, используются и для идентификации браузеров, и для идентификации устройств.

Готовых инструментов для идентификации пользователей мало, но они есть. Попробуем выбрать лучший и максимально универсальный. Будем сравнивать [fingerprintjs2](http://valve.github.io/fingerprintjs2/), [clientjs](https://github.com/jackspirou/clientjs), [cross_browser](https://github.com/Song-Li/cross_browser) и [imprintjs](https://github.com/mattbrailsford/imprintjs). Оценивать эти библиотеки будем по удобству использования, скорости работы и насколько хорошо они умеют отличать пользователей.

Для проверки всех этих библиотек я создал отдельную страничку. Код странички [лежит на github](https://github.com/horechek/fingerprint).

## fingerprintjs2

Первая библиотека - [fingerprintjs2](http://valve.github.io/fingerprintjs2/).

Разработчик fingerprintjs2 рассказывает про устройство своей библиотеки и использования evercookie

<iframe width="950" height="600" src="https://www.youtube.com/embed/zodGHE1d_e0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## clientjs

## cross_browser

## imprintjs

## Выводы

Результаты сравнения

[Panopticlick 3.0](https://www.eff.org/ru/deeplinks/2017/11/panopticlick-30)

<iframe width="950" height="600" src="https://www.youtube.com/embed/3xQLy6lH5OE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## GDRP и другие правовые аспекты

## Ссылки

* [fingerprintjs2](http://valve.github.io/fingerprintjs2/)
* [clientjs](https://github.com/jackspirou/clientjs)
* [cross_browser](https://github.com/Song-Li/cross_browser)
* [imprintjs](https://github.com/mattbrailsford/imprintjs)
* [Technical analysis of client identification mechanisms](https://sites.google.com/a/chromium.org/dev/Home/chromium-security/client-identification-mechanisms)
* [(Cross-)Browser Fingerprinting via OS and Hardware Level Features](http://yinzhicao.org/TrackingFree/crossbrowsertracking_NDSS17.pdf)
* [evercookie](https://github.com/samyk/evercookie)
* [Browser Fingerprint – анонимная идентификация браузеров](https://habr.com/ru/company/oleg-bunin/blog/321294/)
* [amiunique.org](https://amiunique.org/)
* [Browser Fingerprinting – What You Need to Know](https://restoreprivacy.com/browser-fingerprinting/)
* [Общий регламент по защите данных](https://bit.ly/2I3azy6)
* [Device fingerprint](https://en.wikipedia.org/wiki/Device_fingerprint)
* [Panopticlick 3.0](https://www.eff.org/ru/deeplinks/2017/11/panopticlick-30)
* [Remote physical device fingerprinting](https://homes.cs.washington.edu/~yoshi/papers/PDF/KoBrCl05PDF-lowres.pdf)
* [Browser Fingerprinting](https://pixelprivacy.com/resources/browser-fingerprinting/)
* [Web Browser Uniqueness and Fingerprinting](https://medium.com/@ravielakshmanan/web-browser-uniqueness-and-fingerprinting-7eac3c381805)
* [Browser Fingerprint – анонимная идентификация браузеров](https://habr.com/ru/company/oleg-bunin/blog/321294/)