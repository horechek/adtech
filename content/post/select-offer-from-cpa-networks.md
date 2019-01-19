+++
date = "2018-12-30"
title = "Выбор подходящего оффера из списка CPA сетей с использованием API"
slug = "select-offer-from-cpa-networks"
categories = [ "Common" ]
tags = [ "CPA", "Ads" ]
draft = true
+++

CPA расшифровывается как Cost Per Action -- плата за действие: регистрацию на сайте, подписку на новости, пополнение счета и так далее. Такие действия называются конверсиями.

СPA сеть работает как посредник между рекламодателем и площадками. Пользователь переходит по ссылкам, которые предоставляет сеть. Учитываются переходы, совершённые конверсии, владельцам площадок начисляются деньги. CPA сеть берет на себя часть работы. CPA сеть берет на себя часть работы, связанную с учетом статистики и оплаты.

![](/images/cpa/cpa.png)

Рекламодателям выгодно работать по CPA схеме. Результат от показов и кликов измеряется после некоторого времени. С конверсиями очевиднее. Например, пользователь пополнил счет в онлайн казино, тогда владелец площадки, с которой пришел пользователь, получает за это деньги.

Владельцам площадок хочется заработать побольше денег. Для этого подбираются дорогие офферы, которые конвертят. Давайте научимся автоматизировать выбор таких офферов из разных CPA сетей.

Для автоматизации нужны CPA сети с которыми возможна работа через API. Таких сетей не много. Наше приложение будет работать с тремя:

* [Admitad](https://developers.admitad.com/ru/doc/webmaster-api/)
* [Сityads](https://cityads.com/api/dev/interface/rest?lang=ru)
* [Где слон?](https://www.gdeslon.ru/api_settings/xml)

Это не все возможные сети, их десятки. Но у этих вменяемые API с которыми получится интегрироваться. Если вы знаете подходящие сети для интегрирования, напишите мне.

Для этих CPA сетей напишем библиотеки на языке программирования Go.

<h2 id="admitad"><a href="#admitad">Admitad</a></h2>

Для работы нужен список площадок, офферов и баннеров. Нужна статистика по конверсиям. Все это доступно через API Admitad. Методов много, подробно почитать о API можно на [странице для разработчиков](https://developers.admitad.com/ru/doc/api_ru/methods/methods-index/).

> Кампании в терминологии CPA сетей называются офферами или предложениями. В интерфейсе Admitad офферы называются программами. Офферы -- термин, который используется большинством сетей, будем использовать везде, где это уместно.

Первым шагом делаем авторизацию. На [специальной страничке](https://www.admitad.com/ru/webmaster/account/settings/credentials/) получаем идентификатор клиента и ключ для доступа к API.

![](/images/cpa/admitad.png)

Нас интересуют поля `client_id` и `base64_header`. Эти поля используем для получения токена. 

Главной частью библиотеки будет структура `Client`.

```go
type Client struct {
	clientId     string
	base64Header string
	url          string
	client       *http.Client
	token        *Token
	scope        string
}
```

У этого типа реализуем метод `Token`

```go

type Token struct {
	Id           int    `json:"id"`
	Username     string `json:"username"`
	FirstName    string `json:"first_name"`
	LastName     string `json:"last_name"`
	Language     string `json:"language"`
	TokenType    string `json:"token_type"`
	ExpiresIn    int    `json:"expires_in"`
	RefreshToken string `json:"refresh_token"`
	AccessToken  string `json:"access_token"`
	Scope        string `json:"scope"`
}

func (c *Client) Token() (*Token, error) {
	params := url.Values{}
	params.Add("grant_type", "client_credentials")
	params.Add("client_id", c.clientId)
	params.Add("scope", c.scope)
	u := c.url + "/token/?" + params.Encode()

	request, err := http.NewRequest("POST", u, nil)
	if err != nil {
		return nil, err
	}

	request.Header.Add("Authorization", "Basic "+c.base64Header)
	response, err := c.client.Do(request)
	if err != nil {
		return nil, err
	}
	defer response.Body.Close()

	token := &Token{}
	if err := json.NewDecoder(response.Body).Decode(token); err != nil {
		return nil, err
	}

	return token, nil
}
```

Переменная `clientId` и `scope` передаются в URL, `base64Header` используем в этом запросе для авторизации. `scope` указывает к каким частям API нужен доступ.

Добавим функцию, которая установит полученный токен:

```go
func (c *Client) Init(token *Token) {
	c.token = token
}
```

Токен нужен для запросов по кампаниям и баннерами. Для этого напишем функцию, которая будет добавлять поле `AccessToken` из структуры `Token` для авторизиции во все запросам.

```go
func (c *Client) request(u, m string, params url.Values) (*http.Response, error) {
	u = c.url + "/" + u + "/?" + params.Encode()
	request, err := http.NewRequest(m, u, nil)
	if err != nil {
		return nil, err
	}

	request.Header.Add("Authorization", "Bearer "+c.token.AccessToken)
	response, err := c.client.Do(request)
	if err != nil {
		return nil, err
	}

	if response.StatusCode == 200 {
		return response, nil
	}

	defer response.Body.Close()

	e := ApiError{}
	if err := json.NewDecoder(response.Body).Decode(&e); err != nil {
		return nil, err
	}
	return nil, e
}
```

Пример авторизации и получения токена:

```go
var (
    base64Header = "xxx"
    clientId = "xxx"
)

client := NewClient(
    "https://api.admitad.com",
    base64Header,
    clientId,
    []string{"advcampaigns", "banners", "websites", "advcampaigns_for_website", "banners_for_website"},
)

token, err := client.Token()
if err != nil {
    return nil, err
}
client.Init(token)
```

Получаем список баннеров:

```go
func main() {
	client := admitad.NewClient(
		"https://api.admitad.com",
		base64Header,
		clientId,
		[]string{"advcampaigns", "banners", "websites", "advcampaigns_for_website", "banners_for_website"},
	)

	token, err := client.Token()
	if err != nil {
		log.Fatal(err)
	}
	client.Init(token)

	websites := &Websites{}
	err = client.Call("websites", "GET", url.Values{}, websites)
	if err != nil {
		log.Fatal(err)
	}

	banners := []map[string]interface{}{}
	for _, w := range websites.Results {
		p := url.Values{}
		p.Add("limit", "4")
		campaigns := Campaigns{}
		err := client.Call("advcampaigns/website/"+strconv.Itoa(int(w["id"].(float64))), "GET", p, &campaigns)
		if err != nil {
			log.Println(err)
			continue
		}

		for _, c := range campaigns.Results {
			list := &Banners{}
			err := client.Call("banners/"+strconv.Itoa(int(c["id"].(float64)))+"/website/"+strconv.Itoa(int(w["id"].(float64))), "GET", url.Values{}, list)
			if err != nil {
				log.Println(err)
				continue
			}

			banners = append(banners, list.Results...)
		}
	}

	for _, b := range banners {
		fmt.Println("==============")
		for k, v := range b {
			fmt.Println(k+":", v)
		}
	}
}
```

Эта программа покажет список баннеров в виде ключ-значение:

```
==============
id: 69511
name: domain728x90x2
size_width: 728
direct_link: http://ad.admitad.com/g/86eb064e35b72ea2020f0d79a64861/
creation_date: 2014-01-27T13:04:47
banner_image_url: https://cdn.admitad.com/bs/2014/01/27/511635b1ada0fbf9156128cdd37b5a79.gif
type: gif
ecpc: 0.00
mobile_content: false
size_height: 90
html_code: <!-- admitad.banner: 86eb064e35b72ea2020f0d79a64861 Reg.ru -->
<a target="_blank" rel="nofollow" href="http://ad.admitad.com/g/86eb064e35b72ea2020f0d79a64861/?i=4"><img width="728" height="90" border="0" src="http://ad.admitad.com/b/86eb064e35b72ea2020f0d79a64861/" alt="Reg.ru"/></a>
<!-- /admitad.banner -->
is_flash: false
==============
creation_date: 2014-01-27T13:05:01
html_code: <!-- admitad.banner: 02db09fb03b72ea2020f0d79a64861 Reg.ru -->
<a target="_blank" rel="nofollow" href="http://ad.admitad.com/g/02db09fb03b72ea2020f0d79a64861/?i=4"><img width="900" height="90" border="0" src="http://ad.admitad.com/b/02db09fb03b72ea2020f0d79a64861/" alt="Reg.ru"/></a>
<!-- /admitad.banner -->
type: gif
id: 69512
size_width: 900
name: domain900x90
mobile_content: false
direct_link: http://ad.admitad.com/g/02db09fb03b72ea2020f0d79a64861/
size_height: 90
banner_image_url: https://cdn.admitad.com/bs/2014/01/27/33e65232481ba745868f9c34f109bb9f.gif
is_flash: false
ecpc: 0.00
```

В примере используются структуры `Websites`, `Campaigns` и `Banners`. Это похожие структуры  которые создаются на лету или определяются заранее:

```go
type Websites struct {
    Results []map[string]interface{} `json:"results"`
}
    
type Campaigns struct {
    Results []map[string]interface{} `json:"results"`
}

type Banners struct {
    Results []map[string]interface{} `json:"results"`
}
```

Полный код библиотеки для работы с Admitad API выложен на GitHub: [https://github.com/horechek/admitad](https://github.com/horechek/admitad)

<h2 id="cityads"><a href="#cityads">Cityads</a></h2>

Cityads - еще одна CPA сеть. У этой сети API не уступает Admitad. Документации по API много и она доступна в специальном [разделе для разработчиков](https://cityads.com/api/dev/auth?lang=ru).

Начнем с авторизации. Доступы получаем на [специальной страничке](https://cityads.com/ru/webmaster/office/api). Нас интересует поле `remote_auth`.
 
![cityads](/images/cpa/cityads.png)

Авторизация работает или по OAuth, или с использованием `remote_auth`, который получили на сайте. Доступ к API с помощью `remote_auth` проще чем поддержка OAuth, поэтому будем использовать такой подход.

В структуре клиента только три поля.

```go
type Client struct {
	url        string
	remoteAuth string

	client *http.Client
}
```

Метод для HTTP запросов копируем из библиотеки для Admitad один в один, только меняем способ авторизации.

```go
func (c *Client) request(u, m string, params url.Values) (*http.Response, error) {
	params.Add("remote_auth", c.remoteAuth)
	u = c.url + "/" + u + "/?" + params.Encode()
	request, err := http.NewRequest(m, u, nil)
	if err != nil {
		return nil, err
	}

	// дальше все как для Admitad
}
```

Пример получения баннеров: 

```go
package main

import (
	"fmt"
	"log"
	"net/url"

	"github.com/horechek/сityads"
)

func init() {
	log.SetFlags(log.LstdFlags | log.Lshortfile)
}

func main() {
	client := cityads.NewClient(
		"https://cityads.com/api/rest/webmaster/json",
		"xxx",
	)

	type Response struct {
		Status int    `json:"status"`
		Error  string `json:"error"`
		Data   struct {
			Total int `json:"total"`
			Items map[string]struct {
				Id    int    `json:"id"`
				Name  string `json:"name"`
				Items []struct {
					Title     string `json:"title"`
					IsDefault bool   `json:"is_default"`
					DeepLink  string `json:"deep_link"`
				} `json:"items"`
			} `json:"items"`
		} `json:"data"`
	}

	r := Response{}
	params := url.Values{}
	params.Add("user_has_offer", "true")
	err := client.Call("offers/web", "GET", params, &r)
	if err != nil {
		log.Fatal(err)
	}

	for _, offer := range r.Data.Items {
		fmt.Println("==============")
		fmt.Println("name:", offer.Name)
		for _, item := range offer.Items {
			fmt.Println("---")
			fmt.Println("title:", item.Title)
			fmt.Println("is_default:", item.IsDefault)
			fmt.Println("deep_link:", item.DeepLink)
		}
	}
```

Создаем тип `Response` и используем  для парсинга ответа API. Важно заметить, что указан параметр `user_has_offer` -- в этом случае возвращаются только те офферы к которым есть доступ. В структуре `Response` есть поле `DeepLink` -- это ссылка на которую должен идти трафик.

Результат работы программы:

```
==============
name: 220volt
---
title: Стандартная
is_default: true
deep_link: http://pwieu.com/click-JQMOA68R-RMIQCGK9?bt=25&tl=1
==============
name: Ad.House PL: Kredyt Gotowkowy CPL
---
title: Стандартная
is_default: true
deep_link: http://pwieu.com/click-JQMOA6KX-RMIQCEBD?bt=25&tl=1
```

Полный код библиотеки для работы с Сityads API выложен на GitHub: [https://github.com/horechek/cityads](https://github.com/horechek/cityads)

<h2 id="gdeslon"><a href="#gdeslon">Где Слон?</a></h2>

Наш третий кандидат - партнерская сеть "Где Слон?".

![gdeslon](/images/cpa/gdeslon.png)

Создаем экземпляр клиента:

```go
client := gdeslon.NewClient(
    "https://api.gdeslon.ru/api",
    "xxx",
)
```

`xxx` - это ключ доступа

Получаем список баннеров через API

```go
type Response struct {
    Offers []struct {
        Id          uint64 `json:"id"`
        Name        string `json:"name"`
        Description string `json:"description"`
        Url         string `json:"url"`
    } `json:"offers"`
}

r := Response{}
params := url.Values{}
params.Add("q", "книги")
err := client.Call("search.json", "GET", params, &r)
if err != nil {
    log.Fatal(err)
}

for _, offer := range r.Offers {
    fmt.Println("==============")
    fmt.Println("id:", offer.Id)
    fmt.Println("name:", offer.Name)
    fmt.Println("description:", offer.Description)
    fmt.Println("url:", offer.Url)
}
```

Параметр `q` -- набор ключевых слов для поиска офферов.

```
==============
id: 2401857833584176600
name: Детская футболка классическая унисекс Printio Книги
description: Детская футболка классическая унисекс — цвет: белый, пол: Муж. Книги это не мое хобби, это мой способ сбежать из реальности.
url: https://af.gdeslon.ru/cc/c79d32affee0ccdbb778e9c200eddcdcd279dee6/21551cf95bc62ed4
==============
id: 15844375947028713000
name: Футболка классическая Printio Книги
description: Футболка классическая — цвет: белый, пол: Жен, качество: Обычное. Книги это не мое хобби, это мой способ сбежать из реальности.
url: https://af.gdeslon.ru/cc/c79d32affee0ccdbb778e9c200eddcdcd279dee6/dbe287fa0a63d1eb
```

Полный код библиотеки для работы с "Где Слон?" API выложен на GitHub: [https://github.com/horechek/gdeslon](https://github.com/horechek/gdeslon)

<h2 id="spaceship"><a href="#spaceship">Строим космический корабль</a></h2>