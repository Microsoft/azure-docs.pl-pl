---
title: Trasy w usłudze Azure static Web Apps
description: Informacje o regułach routingu zaplecza i sposobach zabezpieczania tras z rolami.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5cbcbcf8914a663a6d039abecd6a4488eaf677b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739648"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Trasy w usłudze Azure static Web Apps Preview

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Routing w usłudze Azure static Web Apps definiuje reguły routingu zaplecza i zachowanie autoryzacji dla zawartości statycznej i interfejsów API<sup>1</sup>. Reguły są definiowane jako tablica reguł w _routes.js_ pliku.

- _routes.js_ pliku musi znajdować się w folderze głównym folderu artefaktów kompilacji aplikacji.
- Reguły są wykonywane w kolejności, w jakiej są wyświetlane w `routes` tablicy.
- Ocena reguły kończy się przy pierwszym dopasowaniu. Reguły routingu nie są powiązane ze sobą.
- Role są zdefiniowane w _routes.jsw_ pliku i użytkownicy są skojarzeni z rolami za pośrednictwem [zaproszeń](authentication-authorization.md).
- Masz pełną kontrolę nad nazwami ról.

Temat dotyczący routingu znacznie pokrywa się z pojęciami dotyczącymi uwierzytelniania i autoryzacji. Upewnij się, że zapoznaj się z przewodnikiem [uwierzytelnianie i autoryzacja](authentication-authorization.md) wraz z tym artykułem.

Zobacz [przykładowy plik trasy](#example-route-file) , aby uzyskać szczegółowe informacje.

## <a name="location"></a>Lokalizacja

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

_routes.js_ pliku musi znajdować się w folderze głównym folderu artefaktów kompilacji aplikacji. Jeśli aplikacja sieci Web zawiera krok kompilacji, który kopiuje skompilowane pliki z określonego folderu do folderu artefaktów kompilacji, wówczas _routes.jsw_ pliku musi istnieć w tym konkretnym folderze.

W poniższej tabeli wymieniono odpowiednie lokalizacje, w których należy umieścić _routes.jsw_ pliku dla wielu platform i bibliotek frontonu.

|Struktura/Biblioteka | Lokalizacja  |
|---------|----------|
| Angular | _zasoby_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

Powyższa tabela jest tylko reprezentatywna dla kilku platform i bibliotek zgodnych z Web Appsami statycznymi platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z tematem [Konfigurowanie platform i bibliotek frontonu](./front-end-frameworks.md) .

## <a name="defining-routes"></a>Definiowanie tras

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Trasy są zdefiniowane w _routes.jsw_ pliku jako tablica reguł tras we `routes` właściwości. Każda reguła składa się ze wzorca trasy wraz z co najmniej jedną opcjonalną właściwością reguły. Zobacz [przykładowy plik tras](#example-route-file) dla przykładów użycia.

| Właściwość reguły  | Wymagane | Wartość domyślna | Komentarz                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Tak      | n/d          | Wzorzec trasy żądany przez wywołującego.<ul><li>[Symbole wieloznaczne](#wildcards) są obsługiwane na końcu ścieżek tras. Na przykład _administrator trasy/ \*_ dopasowuje dowolną trasę pod ścieżką _administratora_ .<li>Domyślny plik trasy to _index.html_.</ul>|
| `serve`        | Nie       | n/d          | Definiuje plik lub ścieżkę zwracaną z żądania. Ścieżka i nazwa pliku mogą się różnić od żądanej ścieżki. Jeśli `serve` wartość nie jest zdefiniowana, zostanie użyta żądana ścieżka. Parametry QueryString nie są obsługiwane; `serve` wartości muszą wskazywać na rzeczywiste pliki.  |
| `allowedRoles` | Nie       | anonimowe     | Tablica nazw ról. <ul><li>Prawidłowe znaki to `a-z` , `A-Z` , `0-9` , i `_` .<li>Wbudowana rola `anonymous` ma zastosowanie do wszystkich nieuwierzytelnionych użytkowników.<li>Wbudowana rola `authenticated` ma zastosowanie do każdego zalogowanego użytkownika.<li>Użytkownicy muszą należeć do co najmniej jednej roli.<li>Role są dopasowane na zasadzie _lub_ . Jeśli użytkownik znajduje się w dowolnej z wymienionych ról, zostanie udzielony dostęp.<li>Indywidualni użytkownicy są skojarzeni z rolami przy użyciu [zaproszeń](authentication-authorization.md).</ul> |
| `statusCode`   | Nie       | 200           | Odpowiedź na [kod stanu HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) dla żądania. |

## <a name="securing-routes-with-roles"></a>Zabezpieczanie tras przy użyciu ról

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Trasy są zabezpieczone przez dodanie co najmniej jednej nazwy roli do `allowedRoles` tablicy reguł. Zobacz [przykładowy plik tras](#example-route-file) dla przykładów użycia.

Domyślnie każdy użytkownik należy do roli wbudowanej `anonymous` , a wszyscy zalogowani użytkownicy są członkami `authenticated` roli. Na przykład aby ograniczyć trasę tylko do uwierzytelnionych użytkowników, należy dodać wbudowaną `authenticated` rolę do `allowedRoles` tablicy.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

W razie konieczności można utworzyć nowe role w `allowedRoles` tablicy. Aby ograniczyć trasę tylko do administratorów, można zdefiniować `administrator` rolę w `allowedRoles` tablicy.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Masz pełną kontrolę nad nazwami ról; nie ma listy, do której Twoje role muszą być zgodne.
- Indywidualni użytkownicy są skojarzeni z rolami przy użyciu [zaproszeń](authentication-authorization.md).

## <a name="wildcards"></a>Symbole wieloznaczne

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Reguły symboli wieloznacznych pasują do wszystkich żądań w ramach danego wzorca trasy. W przypadku zdefiniowania `serve` wartości w regule nazwanego pliku lub ścieżki jest obsługiwana jako odpowiedź.

Na przykład w celu zaimplementowania tras dla aplikacji kalendarza można zmapować wszystkie adresy URL, które znajdują się w trasie _kalendarza_ do obsłużenia pojedynczego pliku.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Plik _calendar.html_ może następnie użyć routingu po stronie klienta, aby zapewnić inny widok dla wariantów adresów URL, takich jak `/calendar/january/1` , `/calendar/2020` i `/calendar/overview` .

Możesz również zabezpieczyć trasy z symbolami wieloznacznymi. W poniższym przykładzie każdy plik żądany w ścieżce _administratora_ wymaga uwierzytelnionego użytkownika, który jest członkiem roli _administratora_ .

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Żądania dotyczące plików, do których odwołuje się obsługiwany plik, są oceniane tylko pod kątem kontroli uwierzytelniania. Na przykład żądania do pliku CSS w ścieżce wieloznacznej umożliwiają użycie pliku CSS, a nie co jest zdefiniowane jako `serve` plik. Plik CSS jest obsługiwany, o ile użytkownik spełnia wymagane wymagania dotyczące uwierzytelniania.

## <a name="fallback-routes"></a>Trasy powrotu

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Aplikacje jednostronicowe, niezależnie od tego, czy korzystają z platform i bibliotek języka JavaScript frontonu, czy platformy zestawów webassembly, takich jak Blazor, często polegają na kierowaniu aplikacji sieci Web po stronie klienta. Te reguły routingu po stronie klienta aktualizują lokalizację okna przeglądarki bez przesyłania żądań z powrotem do serwera. W przypadku odświeżenia strony lub przejścia bezpośrednio do lokalizacji wygenerowanych przez reguły routingu po stronie klienta, wymagana jest trasa rezerwowa po stronie serwera do obsłużynia odpowiedniej strony HTML.

W poniższym przykładzie przedstawiono wspólną trasę rezerwową:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Trasa rezerwowa musi być umieszczona na końcu w regułach routingu, ponieważ przechwytuje wszystkie żądania, które nie zostały przechwycone przez wcześniej zdefiniowane reguły.

## <a name="redirects"></a>Przekierowuje

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Można użyć kodów stanu HTTP [301](https://en.wikipedia.org/wiki/HTTP_301) i [302](https://en.wikipedia.org/wiki/HTTP_302) do przekierowywania żądań z jednej trasy do innej.

Na przykład następująca reguła tworzy przekierowanie 301 z _old-page.html_ do _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Przekierowania również współpracują ze ścieżkami, które nie definiują odrębnych plików.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Niestandardowe strony błędów

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Użytkownicy mogą napotkać różne sytuacje, które mogą spowodować wystąpienie błędu. Korzystając z `platformErrorOverrides` tablicy, można zapewnić niestandardowe środowisko w odpowiedzi na te błędy. Zapoznaj się z [przykładowym plikiem trasy](#example-route-file) do umieszczania tablicy w _routes.js_ pliku.

> [!NOTE]
> Gdy żądanie spowoduje przesłonięcie na poziomie platformy, reguły tras nie są uruchamiane ponownie.

Poniższa tabela zawiera listę dostępnych zastąpień błędów platformy:

| Typ błędu  | Kod stanu HTTP | Opis |
|---------|---------|---------|
| `NotFound` | 404  | Na serwerze nie znaleziono strony. |
| `Unauthenticated` | 401 | Użytkownik nie jest zalogowany przy użyciu [dostawcy uwierzytelniania](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | Konto użytkownika w dostawcy uwierzytelniania nie jest skonfigurowane do udostępniania wymaganych danych. Ten błąd może wystąpić w sytuacji, gdy aplikacja prosi dostawcę uwierzytelniania o adres e-mail użytkownika, ale użytkownik zdecydował się ograniczyć dostęp do tego adresu e-mail. |
| `Unauthorized_InvalidInvitationLink` | 401 | Zaproszenie wygasło lub użytkownik zastosował link do zaproszenia wygenerowanego dla innego odbiorcy.  |
| `Unauthorized_MissingRoles` | 401 | Użytkownik nie jest członkiem wymaganej roli. |
| `Unauthorized_TooManyUsers` | 401 | Lokacja osiągnęła maksymalną liczbę użytkowników, a serwer ogranicza dalsze dodatki. Ten błąd jest narażony na klienta, ponieważ nie ma limitu liczby [zaproszeń](authentication-authorization.md) , które można wygenerować, a niektórzy użytkownicy nigdy nie akceptują zaproszenia.|
| `Unauthorized_Unknown` | 401 | Wystąpił nieznany problem podczas próby uwierzytelnienia użytkownika. Jedną z przyczyn tego błędu może być to, że użytkownik nie został rozpoznany, ponieważ nie udzieli zgody na aplikację.|

## <a name="custom-mime-types"></a>Niestandardowe typy MIME

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

`mimeTypes`Obiekt, który znajduje się na tym samym poziomie co `routes` Tablica, umożliwia kojarzenie [typów MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) z rozszerzeniami plików.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

W powyższym przykładzie wszystkie pliki z `.custom` rozszerzeniem są obsługiwane za pomocą `text/html` typu MIME.

Poniższe zagadnienia są ważne podczas pracy z typami MIME:

- Klucze nie mogą mieć wartości null ani być puste lub mieć więcej niż 50 znaków
- Wartości nie mogą być zerowe ani puste ani mieć więcej niż 1000 znaków

> [!NOTE]
> Statyczne Web Apps rozumie aplikacje Blazor i oczekiwane typy MIME dla plików WASM i DLL, nie trzeba dodawać mapowań dla tych.

## <a name="default-headers"></a>Nagłówki domyślne

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

`defaultHeaders`Obiekt, który znajduje się na tym samym poziomie co `routes` Tablica, umożliwia dodawanie, modyfikowanie lub usuwanie [nagłówków odpowiedzi](https://developer.mozilla.org/docs/Web/HTTP/Headers).

Podanie wartości nagłówka powoduje dodanie lub zmodyfikowanie nagłówka. Podanie pustej wartości spowoduje, że nagłówek zostanie doręczony do klienta.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

W powyższym przykładzie `content-security-policy` zostanie dodany nowy nagłówek, zostanie `cache-control` zmodyfikowana wartość domyślna serwera, a `x-dns-prefetch-control` nagłówek zostanie usunięty.

Poniższe zagadnienia są ważne podczas pracy z nagłówkami:

- Klucze nie mogą mieć wartości null ani być puste.
- Wartości null lub puste usuwają nagłówek z przetwarzania.
- Klucze lub wartości nie mogą zawierać więcej niż 8 000 znaków.
- Zdefiniowane nagłówki są obsługiwane we wszystkich żądaniach.
- Nagłówki zdefiniowane w _routes.js_ dotyczą tylko zawartości statycznej. Można dostosować nagłówki odpowiedzi punktu końcowego interfejsu API w kodzie funkcji.

## <a name="example-route-file"></a>Przykładowy plik trasy

> [!IMPORTANT]
> Funkcje zdefiniowane w *routes.jsw* pliku są obecnie przestarzałe i lepiej zaimplementowane w [pliku konfiguracyjnym](./configuration.md#routes)Web Apps statycznym platformy Azure.

Poniższy przykład pokazuje, jak utworzyć reguły tras dla zawartości statycznej i interfejsów API w _routes.js_ pliku. Niektóre trasy używają [folderu systemowego _/.auth_](authentication-authorization.md) , który uzyskuje dostęp do punktów końcowych powiązanych z uwierzytelnianiem.

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
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

W poniższych przykładach opisano, co się dzieje, gdy żądanie jest zgodne z regułą.

| Żądania do... | Wynik... |
|--|--|--|
| _/Profile_ | Uwierzytelnieni użytkownicy obsługują plik _/profile/index.html_ . Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/admin/reports_ | Użytkownicy uwierzytelnieni w roli _administratorzy_ są obsługiwani _/admin/Reports/index.html_ . Uwierzytelnieni użytkownicy, którzy nie należą do roli _administratorzy_ , są obsługiwani 401 Błąd <sup>2</sup>. Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/api/admin_ | Żądania od uwierzytelnionych użytkowników w roli _administratorzy_ są wysyłane do interfejsu API. Uwierzytelnieni użytkownicy, którzy nie znajdują się w roli _administratorzy_ , a nieuwierzytelnieni użytkownicy są obsługiwani błędem 401. |
| _/customers/contoso_ | Użytkownicy uwierzytelnieni, którzy należą do roli firmy _\_ contoso lub klienci_ _, są_ obsługiwani _/Customers/contoso/index.html_ pliku <sup>2</sup>. Uwierzytelnieni użytkownicy, którzy nie _znajdują się w_ rolach firmy _\_ contoso lub klienci_ , są obsługiwani 401 błędu. Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/Login_ | Nieuwierzytelnionym użytkownikom wzywa się do uwierzytelnienia w usłudze GitHub. |
| _/.auth/login/twitter_ | Autoryzacja za pomocą usługi Twitter jest wyłączona. Serwer reaguje na błąd 404. |
| _/logout_ | Użytkownicy są wyrejestrowani z dowolnego dostawcy uwierzytelniania. |
| _/calendar/2020/01_ | Przeglądarka obsługuje plik _/calendar.html_ . |
| _/specials_ | Przeglądarka zostanie przekierowana do _/Deals_. |
| _/unknown-folder_ | Obsługiwany jest plik _/custom-404.html_ . |
| Pliki z `.custom` rozszerzeniem | Są obsługiwane za pomocą `text/html` typu MIME |

Wszystkie odpowiedzi zawierają `content-security-policy` nagłówki o wartości `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> reguły trasy dla funkcji API obsługują tylko [przekierowywanie](#redirects) i [Zabezpieczanie tras z rolami](#securing-routes-with-roles).

<sup>2</sup> można podać niestandardową stronę błędu, definiując `Unauthorized_MissingRoles` regułę w `platformErrorOverrides` tablicy.

## <a name="restrictions"></a>Ograniczenia

- _routes.jsw_ pliku nie może być większy niż 100 KB
- _routes.jsw_ pliku obsługuje maksymalnie 50 odrębnych ról

Zapoznaj się z [artykułem](quotas.md) dotyczącym przydziału ogólnych ograniczeń i ograniczeń.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania i autoryzacji](authentication-authorization.md)
