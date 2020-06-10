---
title: Trasy w usłudze Azure static Web Apps
description: Informacje o regułach routingu zaplecza i sposobach zabezpieczania tras z rolami.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 84067917a43fc7c84770b8852f11622ffe2af930
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629304"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Trasy w usłudze Azure static Web Apps Preview

Routing w usłudze Azure static Web Apps definiuje reguły routingu zaplecza i zachowanie autoryzacji dla zawartości statycznej i interfejsów API. Reguły są definiowane jako tablica reguł w pliku _Routes. JSON_ .

- Plik _Routes. JSON_ musi znajdować się w folderze głównym folderu artefaktów kompilacji aplikacji.
- Reguły są wykonywane w kolejności, w jakiej są wyświetlane w `routes` tablicy.
- Ocena reguły kończy się przy pierwszym dopasowaniu. Reguły routingu nie są powiązane ze sobą.
- Role są zdefiniowane w pliku _Routes. JSON_ , a użytkownicy są skojarzeni z rolami za pośrednictwem [zaproszeń](authentication-authorization.md).
- Masz pełną kontrolę nad nazwami ról.

Temat dotyczący routingu znacznie pokrywa się z pojęciami dotyczącymi uwierzytelniania i autoryzacji. Upewnij się, że zapoznaj się z przewodnikiem [uwierzytelnianie i autoryzacja](authentication-authorization.md) wraz z tym artykułem.

## <a name="location"></a>Lokalizacja

Plik _Routes. JSON_ musi znajdować się w folderze głównym folderu artefaktów kompilacji aplikacji. Jeśli aplikacja sieci Web zawiera krok kompilacji, który kopiuje skompilowane pliki z określonego folderu do folderu artefaktów kompilacji, plik _Routes. JSON_ musi istnieć w tym konkretnym folderze.

W poniższej tabeli wymieniono odpowiednie lokalizacje umieszczania pliku _Routes. JSON_ dla wielu platform i bibliotek języka JavaScript frontonu.

|Struktura/Biblioteka | Lokalizacja  |
|---------|----------|
| Angular | _stanu_   |
| React   | _public_  |
| Svelte  | _public_   |
| VUE     | _public_ |

## <a name="defining-routes"></a>Definiowanie tras

Trasy są zdefiniowane w pliku _Routes. JSON_ jako tablica reguł tras we `routes` właściwości. Każda reguła składa się ze wzorca trasy wraz z co najmniej jedną opcjonalną właściwością reguły. Zobacz [przykładowy plik tras](#example-route-file) dla przykładów użycia.

| Właściwość reguły  | Wymagane | Wartość domyślna | Komentarz                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Tak      | nie dotyczy          | Wzorzec trasy żądany przez wywołującego.<ul><li>[Symbole wieloznaczne](#wildcards) są obsługiwane na końcu ścieżek tras. Na przykład _administrator trasy/ \* _ dopasowuje dowolną trasę pod ścieżką _administratora_ .<li>Domyślny plik trasy to _index. html_.</ul>|
| `serve`        | Nie       | nie dotyczy          | Definiuje plik lub ścieżkę zwracaną z żądania. Ścieżka i nazwa pliku mogą się różnić od żądanej ścieżki. Jeśli `serve` wartość jest zdefiniowana, zostanie użyta żądana ścieżka. |
| `allowedRoles` | Nie       | anonimowe     | Tablica nazw ról. <ul><li>Prawidłowe znaki to `a-z` , `A-Z` , `0-9` , i `_` .<li>Wbudowana rola `anonymous` ma zastosowanie do wszystkich nieuwierzytelnionych użytkowników.<li>Wbudowana rola `authenticated` ma zastosowanie do każdego zalogowanego użytkownika.<li>Użytkownicy muszą należeć do co najmniej jednej roli.<li>Role są dopasowane na zasadzie _lub_ . Jeśli użytkownik znajduje się w dowolnej z wymienionych ról, zostanie udzielony dostęp.<li>Indywidualni użytkownicy są skojarzeni z rolami za pomocą [zaproszeń](authentication-authorization.md).</ul> |
| `statusCode`   | Nie       | 200           | Odpowiedź na [kod stanu HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) dla żądania. |

## <a name="securing-routes-with-roles"></a>Zabezpieczanie tras przy użyciu ról

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

- Masz pełną kontrolę nad nazwami ról; Brak listy głównej, do której role muszą być zgodne.
- Indywidualni użytkownicy są skojarzeni z rolami przy użyciu [zaproszeń](authentication-authorization.md).

## <a name="wildcards"></a>Symbole wieloznaczne

Reguły symboli wieloznacznych pasują do wszystkich żądań w ramach danego wzorca trasy. W przypadku zdefiniowania `serve` wartości w regule nazwanego pliku lub ścieżki jest obsługiwana jako odpowiedź.

Na przykład w celu zaimplementowania tras dla aplikacji kalendarza można zmapować wszystkie adresy URL, które znajdują się w trasie _kalendarza_ do obsłużenia pojedynczego pliku.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Plik _Calendar. html_ może następnie użyć routingu po stronie klienta, aby zapewnić inny widok dla wariantów adresów URL, takich jak `/calendar/january/1` , `/calendar/2020` i `/calendar/overview` .

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

Struktury i biblioteki języka JavaScript frontonu często bazują na kierowaniu aplikacji sieci Web po stronie klienta. Te reguły routingu po stronie klienta aktualizują lokalizację okna przeglądarki bez przesyłania żądań z powrotem do serwera. W przypadku odświeżenia strony lub przejścia bezpośrednio do lokalizacji wygenerowanych przez reguły routingu po stronie klienta, wymagana jest trasa rezerwowa po stronie serwera do obsłużynia odpowiedniej strony HTML.

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

Można użyć kodów stanu HTTP [301](https://en.wikipedia.org/wiki/HTTP_301) i [302](https://en.wikipedia.org/wiki/HTTP_302) do przekierowywania żądań z jednej trasy do innej.

Na przykład następująca reguła tworzy przekierowanie 301 z _Old-Page. html_ do _New-Page. html_.

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

Użytkownicy mogą napotkać różne sytuacje, które mogą spowodować wystąpienie błędu. Korzystając z `platformErrorOverrides` tablicy, można zapewnić niestandardowe środowisko w odpowiedzi na te błędy. Zapoznaj się z [przykładowym plikiem trasy](#example-route-file) do umieszczania tablicy w pliku _Routes. JSON_ .

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

## <a name="example-route-file"></a>Przykładowy plik trasy

Poniższy przykład pokazuje, jak utworzyć reguły tras dla zawartości statycznej i interfejsów API w pliku _Routes. JSON_ . Niektóre trasy używają [folderu systemowego _/.auth_ ](authentication-authorization.md) , który uzyskuje dostęp do punktów końcowych powiązanych z uwierzytelnianiem.

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
      "statusCode": "301",
      "serve": "/login"
    }
  ]
}
```

W poniższych przykładach opisano, co się dzieje, gdy żądanie jest zgodne z regułą.

|Żądania do...  | Wynik... |
|---------|---------|---------|
| _/Profile_ | Uwierzytelniani użytkownicy są obsługiwani do pliku _/Profile/index.html_ . Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/admin/reports_ | Użytkownicy uwierzytelnieni w roli _administratorzy_ są obsługiwani do pliku _/admin/Reports/index.html_ . Uwierzytelnieni użytkownicy, którzy nie należą do roli _administratorzy_ , są obsługiwani 401 Błąd<sup>1</sup>. Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/api/admin_ | Żądania od uwierzytelnionych użytkowników w roli _administratorzy_ są wysyłane do interfejsu API. Uwierzytelnieni użytkownicy, którzy nie znajdują się w roli _administratorzy_ , a nieuwierzytelnieni użytkownicy są obsługiwani błędem 401. |
| _/customers/contoso_ | Uwierzytelnieni użytkownicy, którzy należą do roli firmy _ \_ contoso lub klienci_ _, są_ obsługiwani do pliku _/Customers/contoso/index.html_ <sup>1</sup>. Uwierzytelnieni użytkownicy, którzy nie _znajdują się w_ rolach firmy _ \_ contoso lub klienci_ , są obsługiwani 401 błędu. Nieuwierzytelnieni użytkownicy przekierowani do programu _/login_. |
| _/Login_     | Nieuwierzytelnionym użytkownikom wzywa się do uwierzytelnienia w usłudze GitHub. |
| _/.auth/login/twitter_     | Autoryzacja za pomocą usługi Twitter jest wyłączona. Serwer reaguje na błąd 404. |
| _/logout_     | Użytkownicy są wyrejestrowani z dowolnego dostawcy uwierzytelniania. |
| _/calendar/2020/01_ | Przeglądarka jest obsługiwana przez plik _/Calendar.html_ . |
| _/specials_ | Przeglądarka zostanie przekierowana do _/Deals_. |
| _/unknown-folder_     | Plik _/Custom-404.html_ jest obsługiwany. |

<sup>1</sup> możesz podać niestandardową stronę błędu, definiując `Unauthorized_MissingRoles` regułę w `platformErrorOverrides` tablicy.

## <a name="restrictions"></a>Ograniczenia

- Plik _Routes. JSON_ nie może mieć więcej niż 100 KB
- Plik _Routes. JSON_ obsługuje maksymalnie 50 odrębnych ról

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania i autoryzacji](authentication-authorization.md)
