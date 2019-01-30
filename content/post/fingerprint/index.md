+++
date = "2019-01-27"
title = "Fingerprint или цифровой отпечаток устройства"
slug = "fingerprint"
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

Fingerprint дословно переводится как отпечатки пальцев. Эти отпечатки используются для идентификации людей. Похожим образом используются цифровые отпечатки - идентифицируют браузеры и устройства в социальных сетях, интернет рекламе, системах аналитики. 

С помощью цифровых отпечатков отличают один браузер от другого или различают устройства. В первом случае у вас поменяется отпечаток когда вы откроете фаерфокс вместо хрома. Во втором случае, даже если вы перейдете в режим инкогнито или откроете другой браузер, отпечаток не поменяется.

Браузерные отпечатки проще - можно использовать меньше параметров и не нужно учитывать различия между браузерами. Отпечатки устройства сложнее, потому что добраться до системной информации устройства не так просто.

По своей сути, отпечатки это закодированная информация о различных параметрах, например максимальное разрешение, установленные шрифты, плагины браузера и так далее.

В статья научимся использовать библиотеки для сбора цифровых отпечатков. Будем использовать [fingerprintjs2](http://valve.github.io/fingerprintjs2/), [clientjs](https://github.com/jackspirou/clientjs), [cross_browser](https://github.com/Song-Li/cross_browser) и [imprintjs](https://github.com/mattbrailsford/imprintjs)

## Как различить браузеры

<iframe width="950" height="600" src="https://www.youtube.com/embed/3xQLy6lH5OE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Как различить устройства

## Как сохранить идентификатор пользователя

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