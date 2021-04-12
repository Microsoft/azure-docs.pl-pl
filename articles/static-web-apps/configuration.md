---
title: Konfigurowanie Web Apps statycznej platformy Azure
description: Dowiedz się, jak skonfigurować trasy, wymuszać reguły zabezpieczeń i ustawienia globalne dla Web Apps statycznej platformy Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 280c13fdee281acc4f805aba27a10277eb3988c2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218978"
---
# <a name="configure-azure-static-web-apps"></a>Konfigurowanie Web Apps statycznej platformy Azure

Konfiguracja Web Apps statycznej platformy Azure jest zdefiniowana w _staticwebapp.config.js_ pliku, który kontroluje następujące ustawienia:

- Routing
- Authentication
- Autoryzacja
- Reguły powrotu
- Zastąpienia odpowiedzi HTTP
- Globalne definicje nagłówków HTTP
- Niestandardowe typy MIME

## <a name="file-location"></a>Lokalizacja pliku

Zalecana lokalizacja dla _staticwebapp.config.js_ jest w folderze ustawionym jako `app_location` [plik przepływu pracy](./github-actions-workflow.md). Jednak plik może być umieszczony w dowolnej lokalizacji w folderze kodu źródłowego aplikacji.

Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> [ _routes.jsw_ pliku](./routes.md) jest ignorowany, jeśli istnieje _staticwebapp.config.js_ .

## <a name="routes"></a>Trasy

Reguły tras umożliwiają zdefiniowanie wzorca adresów URL, które zezwalają na dostęp do aplikacji w sieci Web. Trasy są definiowane jako tablica reguł routingu. Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

- Reguły są zdefiniowane w `routes` tablicy, nawet jeśli istnieje tylko jedna trasa.
- Reguły są wykonywane w kolejności, w jakiej są wyświetlane w `routes` tablicy.
- Ocena reguły zostaje zatrzymana przy pierwszym dopasowaniu — reguły routingu nie są połączone w łańcuchowo.
- Masz pełną kontrolę nad nazwami ról niestandardowych.
  - Istnieje kilka wbudowanych nazw ról, które obejmują [`anonymous`](./authentication-authorization.md) i [`authenticated`](./authentication-authorization.md) .

Zagadnienia dotyczące routingu znacznie się pokrywają z uwierzytelnianiem (identyfikującym użytkownika) i autoryzacją (przypisując możliwości do użytkownika). Upewnij się, że zapoznaj się z przewodnikiem [uwierzytelnianie i autoryzacja](authentication-authorization.md) wraz z tym artykułem.

Domyślny plik zawartości statycznej to plik *index.html* .

## <a name="defining-routes"></a>Definiowanie tras

Każda reguła składa się ze wzorca trasy wraz z co najmniej jedną opcjonalną właściwością reguły. Reguły tras są zdefiniowane w `routes` tablicy. Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

| Właściwość reguły  | Wymagane | Wartość domyślna | Komentarz                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Tak      | n/d          | Wzorzec trasy żądany przez wywołującego.<ul><li>[Symbole wieloznaczne](#wildcards) są obsługiwane na końcu ścieżek tras.<ul><li>Na przykład _administrator trasy/ \*_ dopasowuje dowolną trasę pod ścieżką _administratora_ .</ul></ul>|
| `rewrite`        | Nie       | n/d          | Definiuje plik lub ścieżkę zwracaną z żądania.<ul><li>Wykluczają się wzajemnie z `redirect` reguły<li>Reguły ponownego zapisywania nie zmieniają lokalizacji przeglądarki.<li>Wartości muszą być powiązane z elementem głównym aplikacji</ul>  |
| `redirect`        | Nie       | n/d          | Określa miejsce docelowe przekierowania pliku lub ścieżki dla żądania.<ul><li>Wykluczają się wzajemnie z `rewrite` reguły.<li>Reguły przekierowania zmieniają lokalizację przeglądarki.<li>Domyślny kod odpowiedzi to [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (tymczasowe przekierowanie), ale można przesłonić przy użyciu [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (stałego przekierowania).</ul> |
| `allowedRoles` | Nie       | anonimowe     | Definiuje listę nazw ról wymaganych do uzyskania dostępu do trasy. <ul><li>Prawidłowe znaki to `a-z` , `A-Z` , `0-9` , i `_` .<li>Wbudowana rola, [`anonymous`](./authentication-authorization.md) ma zastosowanie do wszystkich nieuwierzytelnionych użytkowników<li>Wbudowana rola, [`authenticated`](./authentication-authorization.md) ma zastosowanie do każdego zalogowanego użytkownika.<li>Użytkownicy muszą należeć do co najmniej jednej roli.<li>Role są dopasowane na zasadzie _lub_ .<ul><li>Jeśli użytkownik znajduje się w dowolnej z wymienionych ról, zostanie udzielony dostęp.</ul><li>Indywidualni użytkownicy są skojarzeni z rolami przy użyciu [zaproszeń](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | Nie | n/d | Zestaw [nagłówków HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) dodawanych do odpowiedzi. <ul><li>Nagłówki specyficzne dla trasy przesłaniają [`globalHeaders`](#global-headers) , gdy nagłówek specyficzny dla trasy jest taki sam, jak nagłówek globalny w odpowiedzi.<li>Aby usunąć nagłówek, ustaw wartość na pusty ciąg.</ul> |
| `statusCode`   | Nie       | `200`, `301` lub `302` dla przekierowań | [Kod stanu HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) odpowiedzi. |
| `methods` | Nie | Wszystkie metody | Lista metod żądania, które pasują do trasy. Dostępne są następujące metody: `GET` ,,,, `HEAD` ,,,, `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` i `PATCH` . |

Każda właściwość ma określony cel w potoku żądania/odpowiedzi.

| Przeznaczenie | Właściwości |
|---|---|
| Dopasuj trasy | `route`, `methods` |
| Autoryzuj po dopasowaniu trasy | `allowedRoles` |
| Proces po dopasowaniu i autoryzacji reguły | `rewrite` (modyfikuje żądanie) <br><br>`redirect`, `headers` , `statusCode` (modyfikuje odpowiedź) |

## <a name="securing-routes-with-roles"></a>Zabezpieczanie tras przy użyciu ról

Trasy są zabezpieczone przez dodanie co najmniej jednej nazwy roli do `allowedRoles` tablicy reguł, a użytkownicy są skojarzeni z rolami niestandardowymi za pośrednictwem [zaproszeń](./authentication-authorization.md). Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

Domyślnie każdy użytkownik należy do roli wbudowanej `anonymous` , a wszyscy zalogowani użytkownicy są członkami `authenticated` roli.

Na przykład aby ograniczyć trasę tylko do uwierzytelnionych użytkowników, należy dodać wbudowaną `authenticated` rolę do `allowedRoles` tablicy.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

W razie konieczności można utworzyć nowe role w `allowedRoles` tablicy. Aby ograniczyć trasę tylko do administratorów, można zdefiniować własną rolę o nazwie `administrator` , w `allowedRoles` tablicy.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Masz pełną kontrolę nad nazwami ról; nie ma listy, do której Twoje role muszą być zgodne.
- Indywidualni użytkownicy są skojarzeni z rolami przy użyciu [zaproszeń](authentication-authorization.md).

## <a name="wildcards"></a>Symbole wieloznaczne

Reguły symboli wieloznacznych pasują do wszystkich żądań w wzorcu trasy, są obsługiwane tylko na końcu ścieżki i mogą być filtrowane według rozszerzenia pliku. Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

Na przykład aby zaimplementować trasy dla aplikacji kalendarza, można ponownie napisać wszystkie adresy URL, które znajdują się w trasie _kalendarza_ do obsłużenia pojedynczego pliku.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Plik _calendar.html_ może następnie użyć routingu po stronie klienta, aby zapewnić inny widok dla wariantów adresów URL, takich jak `/calendar/january/1` , `/calendar/2020` i `/calendar/overview` .

Można filtrować dopasowania symboli wieloznacznych według rozszerzenia pliku. Na przykład jeśli chcesz dodać regułę, która dopasowuje tylko pliki HTML w danej ścieżce, możesz utworzyć następującą regułę:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Aby filtrować według wielu rozszerzeń plików, należy uwzględnić opcje w nawiasach klamrowych, jak pokazano w poniższym przykładzie:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Typowe przypadki użycia dla wieloznacznych tras obejmują:

- Obsługa określonego pliku dla całego wzorca ścieżki
- Mapowanie różnych metod HTTP na cały wzorzec ścieżki
- Wymuszanie reguł uwierzytelniania i autoryzacji
- Implementowanie wyspecjalizowanych reguł buforowania

## <a name="fallback-routes"></a>Trasy powrotu

Aplikacje jednostronicowe często polegają na routingu po stronie klienta. Te reguły routingu po stronie klienta aktualizują lokalizację okna przeglądarki bez przesyłania żądań z powrotem do serwera. Po odświeżeniu strony lub przejściu bezpośrednio do adresów URL wygenerowanych przez reguły routingu po stronie klienta, wymagana jest trasa rezerwowa po stronie serwera do obsłużynia odpowiedniej strony HTML (która zwykle jest _index.html_ dla aplikacji po stronie klienta).

Aplikację można skonfigurować tak, aby korzystała z reguł implementujących trasy rezerwowe, jak pokazano w poniższym przykładzie, który używa symboli wieloznacznych Path z filtrem plików:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Poniżej przedstawiono przykładową strukturę plików, którą można wykonać przy użyciu tej reguły.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Żądania do... | zwraca... | ze stanem... |
| --- | --- | --- |
| *o* | Plik */index.html* | `200` |
| */images/logo.png* | Plik obrazu  | `200` |
| */images/icon.svg* | Plik */index.html* — ponieważ rozszerzenie pliku *SVG* nie znajduje się na liście w `/images/*.{png,jpg,gif}` filtrze   | `200` |
| */images/unknown.png* | Błąd nieznalezienia pliku  | `404` |
| */css/unknown.css* | Błąd nieznalezienia pliku  | `404` |
| */css/global.css* | Plik arkusza stylów | `200` |
| Każdy inny plik poza folderem */images* lub */CSS* | Plik */index.html* | `200` |

## <a name="global-headers"></a>Nagłówki globalne

`globalHeaders`Sekcja zawiera zestaw [nagłówków HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) stosowanych do każdej odpowiedzi, chyba że jest zastępowany przez regułę [nagłówka trasy](#route-headers) , w przeciwnym razie zwracana jest Unia obu nagłówków z trasy i nagłówków globalnych.

Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

Aby usunąć nagłówek, ustaw wartość na pusty ciąg ( `""` ).

Niektóre typowe przypadki użycia nagłówków globalnych obejmują:

- Niestandardowe reguły buforowania
- Wymuszanie zasad zabezpieczeń
- Ustawienia kodowania

## <a name="response-overrides"></a>Zastąpienia odpowiedzi

`responseOverrides`Sekcja zawiera możliwość zdefiniowania niestandardowej odpowiedzi, gdy serwer w przeciwnym razie zwróci kod błędu. Zobacz [przykładowy plik konfiguracji](#example-configuration-file) , aby poznać przykłady użycia.

Następujące kody HTTP są dostępne do przesłonięcia:

| Kod stanu | Znaczenie | Możliwa przyczyna |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Złe żądanie | Nieprawidłowy link do zaproszenia |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Brak autoryzacji | Żądanie do stron z ograniczeniami podczas nieuwierzytelnionego |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden |<ul><li>Użytkownik jest zalogowany, ale nie ma ról wymaganych do wyświetlenia strony.<li>Użytkownik jest zalogowany, ale środowisko uruchomieniowe nie może pobrać szczegółów użytkownika z ich oświadczeń tożsamości.<li>Zbyt wielu użytkowników zalogował się w witrynie przy użyciu ról niestandardowych, dlatego środowisko uruchomieniowe nie może zalogować użytkownika.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Nie znaleziono | Nie znaleziono pliku |

W poniższym przykładzie pokazano, jak zastąpić kod błędu.

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>Przykładowy plik konfiguracji

```json
{
    "routes": [
        {
            "route": "/profile",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/admin/*",
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    }
}
```

W oparciu o powyższą konfigurację zapoznaj się z następującymi scenariuszami.

| Żądania do... | wyniki w... |
| --- | --- |
| _/Profile_ | Uwierzytelnieni użytkownicy obsługują plik _/profile/index.html_ . Nieuwierzytelniani użytkownicy są przekierowywani do programu _/login_. |
| _/admin_ | Uwierzytelnieni użytkownicy w roli _administrator_ obsługują plik _/admin/index.html_ . Uwierzytelnieni użytkownicy, którzy nie należą do roli _administratora_ , są z `403` błędem <sup>1</sup>. Nieuwierzytelniani użytkownicy są przekierowywani do programu _/login_. |
| _/logo.png_ | Program udostępnia obraz z niestandardową regułą pamięci podręcznej, w której maksymalny wiek jest nieco dłuższy niż 182 dni (15 770 000 s). |
| _/api/admin_ | `GET` żądania od uwierzytelnionych użytkowników w roli _registeredusers_ są wysyłane do interfejsu API. Uwierzytelnieni użytkownicy, którzy nie znajdują się w roli _registeredusers_ , a nieuwierzytelnieni użytkownicy są obsługiwani `401` błędem.<br/><br/>`POST`, `PUT` , `PATCH` i `DELETE` żądania od użytkowników uwierzytelnionych w roli _administratora_ są wysyłane do interfejsu API. Uwierzytelnieni użytkownicy, którzy nie znajdują się w roli _administratora_ , a nieuwierzytelnieni użytkownicy są obsługiwani `401` błędem. |
| _/customers/contoso_ | Uwierzytelniani użytkownicy, którzy należą do roli firmy _\_ contoso lub klienci_ _, są_ obsługiwani _/Customers/contoso/index.html_ . Uwierzytelnieni użytkownicy, którzy nie znajdują się w roli _administratora_ lub _klienci firmy \_ contoso_ , są obsługiwani `403` błędem <sup>1</sup>. Nieuwierzytelniani użytkownicy są przekierowywani do programu _/login_. |
| _/Login_ | Nieuwierzytelnionym użytkownikom wzywa się do uwierzytelnienia w usłudze GitHub. |
| _/.auth/login/twitter_ | Ponieważ autoryzacja w usłudze Twitter jest wyłączona przez regułę trasy, `404` zwracany jest błąd, który powraca do obsługi _/index.html_ z `200` kodem stanu. |
| _/logout_ | Użytkownicy są wyrejestrowani z dowolnego dostawcy uwierzytelniania. |
| _/calendar/2021/01_ | Przeglądarka obsługuje plik _/calendar.html_ . |
| _/specials_ | Przeglądarka zostanie trwale przekierowana do _/Deals_. |
| _/data.jsna_ | Plik obsługiwany przy użyciu `text/json` typu MIME. |
| _/about_ lub dowolna z folderów, które pasują do wzorców routingu po stronie klienta | Plik _/index.html_ jest obsługiwany przy użyciu `200` kodu stanu. |
| Nieistniejący plik w folderze _/images/_ | `404`Wystąpił błąd. |

<sup>1</sup> możesz podać niestandardową stronę błędu za pomocą [reguły przesłaniania odpowiedzi](#response-overrides).

## <a name="restrictions"></a>Ograniczenia

Istnieją następujące ograniczenia dotyczące _staticwebapps.config.js_ pliku.

- Maksymalny rozmiar pliku to 100 KB
- Maksymalnie 50 odrębnych ról

Zapoznaj się z [artykułem](quotas.md) dotyczącym przydziału ogólnych ograniczeń i ograniczeń.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania i autoryzacji](authentication-authorization.md)
