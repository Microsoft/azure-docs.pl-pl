---
title: Ograniczenia URI przekierowania (adres URL odpowiedzi) | Azure
titleSuffix: Microsoft identity platform
description: Opis ograniczeń i ograniczeń w formacie URI przekierowania (adres URL odpowiedzi) wymuszonego przez platformę tożsamości firmy Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 91df89a69368056c1967e641562cf8515f44ade0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582812"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Ograniczenia i ograniczenia URI przekierowania (adres URL odpowiedzi)

Identyfikator URI przekierowania lub adres URL odpowiedzi to lokalizacja, do której serwer autoryzacji wysyła użytkownika po pomyślnym autoryzacji aplikacji i przydzieleniu mu kodu autoryzacji lub tokenu dostępu. Serwer autoryzacji wysyła kod lub token do identyfikatora URI przekierowania, dlatego ważne jest, aby zarejestrować poprawną lokalizację w ramach procesu rejestracji aplikacji.

 Identyfikatory URI przekierowania dotyczą następujących ograniczeń:

* Identyfikator URI przekierowania musi rozpoczynać się od schematu `https` . Istnieją pewne wyjątki dotyczące identyfikatorów URI przekierowania [hosta lokalnego](#localhost-exceptions) .

* W identyfikatorze URI przekierowania jest rozróżniana wielkość liter. Jego wielkość liter musi być zgodna z wielkością liter w ścieżce URL działającej aplikacji. Na przykład jeśli aplikacja zawiera jako część swojej ścieżki, nie należy `.../abc/response-oidc` określać jej `.../ABC/response-oidc` w identyfikatorze URI przekierowania. Ponieważ przeglądarka sieci Web traktuje ścieżki w miarę uwzględniania wielkości liter, pliki cookie skojarzone z programem `.../abc/response-oidc` mogą zostać wykluczone w przypadku przekierowania do niezgodnego z wielkością liter `.../ABC/response-oidc` adresów URL.

## <a name="maximum-number-of-redirect-uris"></a>Maksymalna liczba identyfikatorów URI przekierowania

W tej tabeli przedstawiono maksymalną liczbę identyfikatorów URI przekierowania, które można dodać do rejestracji aplikacji na platformie tożsamości firmy Microsoft.

| Konta, które są zalogowane | Maksymalna liczba identyfikatorów URI przekierowania | Opis |
|--------------------------|---------------------------------|-------------|
| Konta służbowe firmy Microsoft w ramach dzierżawy Azure Active Directory (Azure AD) w organizacji | 256 | `signInAudience`pole w manifeście aplikacji ma ustawioną wartość *AzureADMyOrg* lub *AzureADMultipleOrgs* . |
| Osobiste konta Microsoft i służbowe konta służbowe | 100 | `signInAudience` pole w manifeście aplikacji jest ustawione na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maksymalna długość identyfikatora URI

Dla każdego identyfikatora URI przekierowania, który można dodać do rejestracji aplikacji, można użyć maksymalnie 256 znaków.

## <a name="supported-schemes"></a>Obsługiwane schematy

Model aplikacji Azure Active Directory (Azure AD) obsługuje obecnie zarówno schematy HTTP, jak i HTTPS dla aplikacji, które logują się w ramach konta służbowego w ramach dzierżawy usługi Azure AD w organizacji. Te typy kont są określane przez `AzureADMyOrg` wartości i `AzureADMultipleOrgs` w `signInAudience` polu manifestu aplikacji. W przypadku aplikacji, które logują się do osobistych kont Microsoft (MSA) *i* kont służbowych (czyli `signInAudience` ustawionych jako `AzureADandPersonalMicrosoftAccount` ), dozwolony jest tylko schemat https.

Aby dodać identyfikatory URI przekierowania ze schematem HTTP do rejestracji aplikacji, które logują się do kont służbowych, użyj edytora manifestu aplikacji w [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) w Azure Portal. Jednak chociaż można ustawić identyfikator URI przekierowania oparty na protokole HTTP przy użyciu edytora manifestu, *zdecydowanie* zalecamy użycie schematu HTTPS dla identyfikatorów URI przekierowania.

## <a name="localhost-exceptions"></a>Wyjątki localhost

Zgodnie z [sekcją RFC 8252 sekcje 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) i [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), "sprzężenie zwrotne" lub "localhost" identyfikatorów URI przekierowania są dwa specjalne zagadnienia:

1. `http` Schematy identyfikatorów URI są akceptowalne, ponieważ przekierowanie nigdy nie opuszcza urządzenia. W związku z tym oba te identyfikatory URI są dopuszczalne:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Ze względu na tymczasowe zakresy portów często wymagane przez natywne aplikacje, składnik portu (na przykład `:5001` lub `:443` ) jest ignorowany na potrzeby dopasowywania identyfikatora URI przekierowania. W związku z tym wszystkie te identyfikatory URI są uważane za równoważne:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Z punktu widzenia projektowania oznacza to kilka rzeczy:

* Nie należy rejestrować wielu identyfikatorów URI przekierowania, gdy tylko port jest różny. Serwer logowania wybiera arbitralnie i użyje zachowania skojarzonego z tym identyfikatorem URI przekierowania (na przykład, czy jest to `web` -, `native` -lub `spa` przekierowania).

    Jest to szczególnie ważne, gdy chcesz użyć różnych przepływów uwierzytelniania w tej samej rejestracji aplikacji, na przykład w przypadku przydzielenia kodu autoryzacji i niejawnego przepływu. Aby skojarzyć poprawne zachowanie odpowiedzi z każdym identyfikatorem URI przekierowania, serwer logowania musi być w stanie rozróżnić identyfikatory URI przekierowania i nie może tego zrobić, gdy tylko port jest różny.
* Aby zarejestrować wiele identyfikatorów URI przekierowania na hoście lokalnym w celu przetestowania różnych przepływów podczas opracowywania, Odróżnij je za pomocą składnika *ścieżki* identyfikatora URI. Na przykład `http://localhost/MyWebApp` nie są zgodne `http://localhost/MyNativeApp` .
* Adres sprzężenia zwrotnego IPv6 ( `[::1]` ) nie jest obecnie obsługiwany.

#### <a name="prefer-127001-over-localhost"></a>Preferuj adres 127.0.0.1 przez localhost

Aby zapobiec utracie aplikacji przez błędnie skonfigurowane zapory lub zmienić nazwy interfejsów sieciowych, użyj adresu sprzężenia zwrotnego literału IP `127.0.0.1` w identyfikatorze URI przekierowania zamiast `localhost` . Na przykład `https://127.0.0.1`.

Nie można jednak użyć pola tekstowego **Przekieruj identyfikatory URI** w Azure Portal, aby dodać URI przekierowania na podstawie sprzężenia zwrotnego, który używa `http` schematu:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Okno dialogowe błędów w Azure Portal pokazujące niedozwolony identyfikator URI przekierowania sprzężenia zwrotnego opartego na protokole http":::

Aby dodać identyfikator URI przekierowania, który używa `http` schematu z `127.0.0.1` adresem sprzężenia zwrotnego, należy obecnie zmodyfikować atrybut [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) w [manifeście aplikacji](reference-app-manifest.md).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Ograniczenia dotyczące symboli wieloznacznych w identyfikatorach URI przekierowania

Identyfikatory URI z symbolami wieloznacznymi `https://*.contoso.com` mogą pozornie być wygodne, ale należy je unikać z powodu implikacji zabezpieczeń. Zgodnie ze specyfikacją OAuth 2,0 ([sekcja 3.1.2 specyfikacji RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.

Symbole wieloznaczne nie są obecnie obsługiwane w rejestracjach aplikacji skonfigurowanych do logowania osobistych kont Microsoft i kont służbowych. Symbole wieloznaczne są dozwolone, jednak w przypadku aplikacji skonfigurowanych do logowania tylko kont służbowych w dzierżawie usługi Azure AD w organizacji.

Aby dodać identyfikatory URI przekierowania z symbolami wieloznacznymi do rejestracji aplikacji, które logują się na kontach służbowych, użyj edytora manifestu aplikacji w [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) w Azure Portal. Chociaż istnieje możliwość ustawienia identyfikatora URI przekierowania z symbolem wieloznacznym przy użyciu edytora manifestu, *zdecydowanie* zalecamy przestrzeganie [sekcji 3.1.2 RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) i użycie tylko bezwzględnych identyfikatorów URI.

Jeśli scenariusz wymaga więcej identyfikatorów URI przekierowania niż maksymalny dozwolony limit, rozważ użycie następującego [parametru stanu](#use-a-state-parameter) zamiast dodawania wieloznacznego identyfikatora URI przekierowania.

#### <a name="use-a-state-parameter"></a>Użyj parametru stanu

Jeśli masz kilka poddomen i twój scenariusz wymaga, aby po pomyślnym uwierzytelnieniu przekierować użytkowników na tę samą stronę, z której uruchomiono, przy użyciu parametru stanu może być przydatne.

W tym podejściu:

1. Utwórz "współużytkowany" identyfikator URI przekierowania dla aplikacji, aby przetworzyć tokeny zabezpieczające otrzymane z punktu końcowego autoryzacji.
1. Aplikacja może wysyłać parametry specyficzne dla aplikacji (takie jak adres URL poddomeny, w którym użytkownik wychodził lub coś, jak informacje o znakowaniu) w parametrze State. W przypadku korzystania z parametru stanu Ochrona przed CSRF ochroną zgodnie z opisem w [sekcji 10,12 specyfikacji RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. Parametry specyficzne dla aplikacji będą zawierać wszystkie informacje potrzebne do przetworzenia przez aplikację poprawnego środowiska dla użytkownika, czyli konstruowania odpowiedniego stanu aplikacji. Punkt końcowy autoryzacji usługi Azure AD przydzieli kod HTML z parametru State, aby upewnić się, że nie przekazujesz zawartości HTML w tym parametrze.
1. Gdy usługa Azure AD wysyła odpowiedź do "udostępnionego" identyfikatora URI przekierowania, wyśle parametr stanu z powrotem do aplikacji.
1. Aplikacja może następnie użyć wartości w parametrze State, aby określić adres URL, do którego ma być wysyłany użytkownik. Upewnij się, że jest sprawdzana poprawność ochrony CSRF.

> [!WARNING]
> Takie podejście pozwala naruszonemu klientowi zmodyfikować dodatkowe parametry wysłane w parametrze State, w związku z tym przekierowując użytkownika do innego adresu URL, który jest [otwartym zagrożeniem](https://tools.ietf.org/html/rfc6819#section-4.2.4) w dokumencie RFC 6819. W związku z tym klient musi chronić te parametry przez szyfrowanie stanu lub Weryfikowanie go w inny sposób, na przykład w celu sprawdzenia nazwy domeny w identyfikatorze URI przekierowania względem tokenu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [manifestu aplikacji](reference-app-manifest.md)do rejestracji aplikacji.
