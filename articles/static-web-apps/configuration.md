---
title: Konfigurowanie Azure Static Web Apps
description: Dowiedz się, jak konfigurować trasy, wymuszać reguły zabezpieczeń i ustawienia globalne dla Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502287"
---
# <a name="configure-azure-static-web-apps"></a>Konfigurowanie Azure Static Web Apps

Konfiguracja Azure Static Web Apps jest zdefiniowana w _staticwebapp.config.jspliku,_ który kontroluje następujące ustawienia:

- Routing
- Authentication
- Autoryzacja
- Reguły rezerwowe
- Przesłonięcia odpowiedzi HTTP
- Globalne definicje nagłówka HTTP
- Niestandardowe typy MIME

## <a name="file-location"></a>Lokalizacja pliku

Zalecana lokalizacja dlastaticwebapp.config.js _znajduje_ się w folderze ustawionym jako w `app_location` pliku [przepływu pracy](./github-actions-workflow.md). Plik może jednak zostać umieszczony w dowolnej lokalizacji w folderze kodu źródłowego aplikacji.

Zobacz [przykładowy plik konfiguracji,](#example-configuration-file) aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Jeśli [ _routes.jsw pliku_](./routes.md) istniejestaticwebapp.config.jsjest _ignorowana._

## <a name="routes"></a>Trasy

Reguły tras umożliwiają zdefiniowanie wzorca adresów URL, które zezwalają na dostęp do aplikacji w Internecie. Trasy są definiowane jako tablica reguł routingu. Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

- Reguły są definiowane w `routes` tablicy, nawet jeśli masz tylko jedną trasę.
- Reguły są wykonywane w kolejności, w jak są wyświetlane w `routes` tablicy.
- Ocena reguły zatrzymuje się przy pierwszym dopasowaniu — reguły routingu nie są ze sobą połączone.
- Masz pełną kontrolę nad niestandardowymi nazwami ról.
  - Istnieje kilka wbudowanych nazw ról, takich jak [`anonymous`](./authentication-authorization.md) i [`authenticated`](./authentication-authorization.md) .

Kwestie związane z routingiem znacząco nakładają się na pojęcia związane z uwierzytelnianiem (identyfikowaniem użytkownika) i autoryzacją (przypisywaniem możliwości do użytkownika). Pamiętaj, aby przeczytać przewodnik [uwierzytelniania i autoryzacji](authentication-authorization.md) wraz z tym artykułem.

Domyślnym plikiem zawartości statycznej _jestindex.html._

## <a name="defining-routes"></a>Definiowanie tras

Każda reguła składa się ze wzorca trasy wraz z co najmniej jedną opcjonalną właściwością reguły. Reguły tras są definiowane w `routes` tablicy. Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

| Właściwość reguły                       | Wymagane | Wartość domyślna                        | Komentarz                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Tak      | n/d                                  | Wzorzec trasy żądany przez wywołujący.<ul><li>[Symbole wieloznaczne](#wildcards) są obsługiwane na końcu ścieżek tras.<ul><li>Na przykład administrator _trasy/ dopasowuje \*_ dowolną trasę w ramach ścieżki _administratora._</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Nie       | n/d                                  | Definiuje plik lub ścieżkę zwróconą z żądania.<ul><li>Wzajemnie wyklucza się z `redirect` regułą<li>Reguły ponownego agwania nie zmieniają lokalizacji przeglądarki.<li>Wartości muszą być względne względem katalogu głównego aplikacji</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Nie       | n/d                                  | Definiuje miejsce docelowe przekierowania pliku lub ścieżki dla żądania.<ul><li>Wyklucza się wzajemnie z `rewrite` regułą.<li>Reguły przekierowania zmieniają lokalizację przeglądarki.<li>Domyślny kod odpowiedzi to [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (przekierowywanie tymczasowe), ale można go przesłonić za pomocą [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (trwałego przekierowania).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Nie       | Anonimowy                            | Definiuje listę nazw ról wymaganych do uzyskania dostępu do trasy. <ul><li>Prawidłowe znaki to `a-z` , `A-Z` , i `0-9` `_` .<li>Wbudowana rola, [`anonymous`](./authentication-authorization.md) , ma zastosowanie do wszystkich nieuwierzytanych użytkowników<li>Wbudowana rola ma zastosowanie [`authenticated`](./authentication-authorization.md) do każdego zalogowanego użytkownika.<li>Użytkownicy muszą należeć do co najmniej jednej roli.<li>Role są do siebie dopasowane na podstawie _OR._<ul><li>Jeśli użytkownik należy do dowolnej z wymienionych ról, zostanie udzielony dostęp.</ul><li>Indywidualni użytkownicy są skojarzone z rolami [za pośrednictwem zaproszeń.](authentication-authorization.md)</ul> |
| `headers`<a id="route-headers"></a> | Nie       | n/d                                  | Zestaw [nagłówków HTTP dodanych](https://developer.mozilla.org/docs/Web/HTTP/Headers) do odpowiedzi. <ul><li>Nagłówki specyficzne dla trasy zastępują, gdy nagłówek specyficzny dla trasy jest taki sam jak [`globalHeaders`](#global-headers) nagłówek globalny w odpowiedzi.<li>Aby usunąć nagłówek, ustaw wartość na pusty ciąg.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Nie       | `200`, `301` lub `302` dla przekierowań | Kod [stanu HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) odpowiedzi.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Nie       | Wszystkie metody                          | Lista metod żądań, które pasują do trasy. Dostępne metody to: `GET` , , , , , , , , `HEAD` i `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Każda właściwość ma określony cel w potoku żądania/odpowiedzi.

| Przeznaczenie                                        | Właściwości                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Dopasowanie tras                                   | `route`, `methods`                                                                           |
| Autoryzacja po dopasowaniu trasy             | `allowedRoles`                                                                               |
| Przetwarzanie po dopasowaniu i autoryzowaniu reguły | `rewrite` (modyfikuje żądanie) <br><br>`redirect`, `headers` , `statusCode` (modyfikuje odpowiedź) |

## <a name="securing-routes-with-roles"></a>Zabezpieczanie tras za pomocą ról

Trasy są zabezpieczone przez dodanie co najmniej jednej nazwy roli do tablicy reguł, a użytkownicy są skojarzone z rolami niestandardowymi za `allowedRoles` [pośrednictwem zaproszeń.](./authentication-authorization.md) Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

Domyślnie każdy użytkownik należy do wbudowanej roli, a wszyscy zalogowani użytkownicy są `anonymous` jej `authenticated` członkami.

Aby na przykład ograniczyć trasę tylko do uwierzytelnionych użytkowników, dodaj wbudowaną `authenticated` rolę do `allowedRoles` tablicy.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

W razie potrzeby w tablicy można tworzyć nowe `allowedRoles` role. Aby ograniczyć trasę tylko do administratorów, możesz zdefiniować własną rolę o nazwie `administrator` w `allowedRoles` tablicy .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Masz pełną kontrolę nad nazwami ról. nie ma listy, do której role muszą być przestrzegane.
- Poszczegnieni użytkownicy są skojarzone z [rolami za pośrednictwem zaproszeń.](authentication-authorization.md)

## <a name="wildcards"></a>Symbole wieloznaczne

Reguły symboli wieloznacznych pasują do wszystkich żądań we wzorcu trasy, są obsługiwane tylko na końcu ścieżki i mogą być filtrowane według rozszerzenia pliku. Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

Na przykład aby zaimplementować trasy dla aplikacji kalendarza, można ponownie napisać  wszystkie adresy URL, które należą do trasy kalendarza, aby obsługiwać pojedynczy plik.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Plik _calendar.html_ może następnie używać routingu po stronie klienta, aby obsługiwać inny widok dla odmian adresów `/calendar/january/1` URL, takich jak , i `/calendar/2020` `/calendar/overview` .

Dopasowania symboli wieloznacznych można filtrować według rozszerzenia pliku. Jeśli na przykład chcesz dodać regułę, która pasuje tylko do plików HTML w danej ścieżce, możesz utworzyć następującą regułę:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Aby filtrować według wielu rozszerzeń plików, należy uwzględnić opcje w nawiasach klamrowych, jak pokazano w tym przykładzie:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Typowe przypadki użycia tras z symbolami wieloznacznych obejmują:

- Obsługa określonego pliku dla całego wzorca ścieżki
- Mapowanie różnych metod HTTP na cały wzorzec ścieżki
- Wymuszanie reguł uwierzytelniania i autoryzacji
- Implementowanie wyspecjalizowanych reguł buforowania

## <a name="fallback-routes"></a>Trasy powrotu

Aplikacje jednostronicowe często polegają na routingu po stronie klienta. Te reguły routingu po stronie klienta aktualizują lokalizację okna przeglądarki bez zwracania żądań do serwera. W przypadku odświeżenia strony lub bezpośredniego przechodzenia do adresów URL generowanych przez reguły routingu po stronie klienta do obsługi odpowiedniej strony HTML (zazwyczaj jest toindex.htm _l_ dla aplikacji po stronie klienta) wymagana jest trasa rezerwowa po stronie serwera.

Możesz skonfigurować aplikację do używania reguł, które implementują trasę powrotu, jak pokazano w poniższym przykładzie, który używa symbolu wieloznacznego ścieżki z filtrem plików:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

W poniższej przykładowej strukturze plików możliwe są następujące wyniki dla tej reguły.

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

| Żądania do...                                         | Zwraca...                                                                                                    | ze stanem... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | Plik _/index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | Plik obrazu                                                                                                | `200`              |
| _/images/icon.svg_                                     | Plik _/index.html_ — ponieważ rozszerzenie _pliku svg_ nie jest wymienione w `/images/*.{png,jpg,gif}` filtrze | `200`              |
| _/images/unknown.png_                                  | Błąd Nie znaleziono pliku                                                                                          | `404`              |
| _/css/unknown.css_                                     | Błąd Nie znaleziono pliku                                                                                          | `404`              |
| _/css/global.css_                                      | Plik arkusza stylów                                                                                           | `200`              |
| Dowolny inny plik poza _folderami /images_ _lub /css_ | Plik _/index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>Nagłówki globalne

Sekcja zawiera zestaw nagłówków HTTP zastosowanych do każdej odpowiedzi, chyba że zostanie zastąpiony przez regułę nagłówka trasy. W przeciwnym razie zostanie zwrócona unii zarówno nagłówków z trasy, jak i nagłówków `globalHeaders` globalnych. [](https://developer.mozilla.org/docs/Web/HTTP/Headers) [](#route-headers)

Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

Aby usunąć nagłówek, ustaw wartość na pusty ciąg ( `""` ).

Niektóre typowe przypadki użycia nagłówków globalnych to:

- Niestandardowe reguły buforowania
- Wymuszanie zasad zabezpieczeń
- Ustawienia kodowania

## <a name="response-overrides"></a>Przesłonięcia odpowiedzi

Sekcja umożliwia zdefiniowanie odpowiedzi niestandardowej, gdy serwer zwróci `responseOverrides` kod błędu. Przykłady [użycia można znaleźć w przykładowym pliku](#example-configuration-file) konfiguracji.

Następujące kody HTTP są dostępne do zastąpienia:

| Kod stanu                                                   | Znaczenie      | Możliwa przyczyna                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Złe żądanie  | Nieprawidłowy link zaproszenia                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Brak autoryzacji | Żądanie do stron z ograniczeniami podczas nieuwierzytania                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden    | <ul><li>Użytkownik jest zalogowany, ale nie ma ról wymaganych do wyświetlenia strony.<li>Użytkownik jest zalogowany, ale środowisko uruchomieniowe nie może pobrać szczegółów użytkownika ze swoich oświadczeń tożsamości.<li>Zbyt wielu użytkowników loguje się do witryny przy użyciu ról niestandardowych, dlatego środowisko uruchomieniowe nie może zalogować użytkownika.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Nie znaleziono    | Nie znaleziono pliku                                                                                                                                                                                                                                                                                     |

W poniższej przykładowej konfiguracji pokazano, jak zastąpić kod błędu.

```json
{
  "responseOverrides": {
    "400": {
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
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
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
    "400": {
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

Na podstawie powyższej konfiguracji przejrzyj następujące scenariusze.

| Żądania do...                                                    | powoduje...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | Uwierzytelnionym użytkownikom jest obsługiwany _plik /profile/index.html._ Nieuwierzytani użytkownicy są przekierowywani do _/login_.                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | Uwierzytelnionym użytkownikom w _roli administratora_ jest obsługiwany _plik /admin/index.html._ Uwierzytelnionym użytkownikom, którzy nie mają _roli administratora,_ jest wyświetlany `403` błąd <sup>1.</sup> Nieuwierzytani użytkownicy są przekierowywani do _/login_.                                                                                                                                                                                                          |
| _/logo.png_                                                       | Obsługuje obraz przy użyciu niestandardowej reguły pamięci podręcznej, w której maksymalny wiek wynosi nieco ponad 182 dni (15 770 000 sekund).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET` Żądania od uwierzytelnionych użytkowników w _roli zarejestrowanychużytkowcy_ są wysyłane do interfejsu API. Uwierzytelnieni użytkownicy, którzy nie _mają roli zarejestrowaniużytkowcy,_ i nieuwierzytowieni użytkownicy są obarczeni `401` błędem.<br/><br/>`POST`Żądania , , i od uwierzytelnionych użytkowników w `PUT` roli `PATCH` administratora są wysyłane do `DELETE` interfejsu  API. Uwierzytelnieni użytkownicy, którzy nie mają _roli administratora,_ i nieuwierzytowieni użytkownicy mają komunikat o `401` błędzie. |
| _/customers/contoso_                                              | Uwierzytelnieni użytkownicy, którzy  należą do administratora lub customers_contoso _ról,_ są adresani do pliku _/customers/contoso/index.html._ Uwierzytelnieni użytkownicy, którzy nie znajdują się _w roli administratora_ lub _customers_contoso,_ są o `403` <sup>błędzie 1.</sup> Nieuwierzytani użytkownicy są przekierowywani do _/login_.                                                                                                                            |
| _/login_                                                          | Uwierzytelnienie nieuwierzytowanych użytkowników w usłudze GitHub jest trudne.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Ponieważ autoryzacja za pomocą konta Twitter jest wyłączona przez regułę trasy, jest zwracany błąd, który powraca do obsługi `404` _/index.html_ z `200` kodem stanu.                                                                                                                                                                                                                                                                                     |
| _/logout (wyloguj się)_                                                         | Użytkownicy są wylogowani z dowolnego dostawcy uwierzytelniania.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | Przeglądarka jest obsługiwany _plik /calendar.html._                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | Przeglądarka jest trwale przekierowywany do _/deals_.                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jsna_                                                      | Plik obsługiwany za pomocą `text/json` typu MIME.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_ lub dowolny folder, który pasuje do wzorców routingu po stronie klienta | Plik _/index.html_ jest obsługiwany z `200` kodem stanu.                                                                                                                                                                                                                                                                                                                                                                                    |
| Nieistniejący plik w _folderze /images/_                     | `404`Błąd.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> Niestandardową stronę błędu można podać przy użyciu reguły [przesłonięcia odpowiedzi](#response-overrides).

## <a name="restrictions"></a>Ograniczenia

Istnieją następujące ograniczenia dotyczącestaticwebapps.config.js _pliku._

- Maksymalny rozmiar pliku to 100 KB
- Maksymalnie 50 odrębnych ról

Zobacz artykuł [Limity przydziału,](quotas.md) aby uzyskać ogólne ograniczenia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania i autoryzacji](authentication-authorization.md)
