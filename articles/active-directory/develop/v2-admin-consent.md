---
title: Protokoły zgody administratora platformy tożsamości firmy Microsoft
description: Opis autoryzacji na platformie tożsamości firmy Microsoft, w tym zakresów, uprawnień i zgody.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b6fb5f680dfa5e2c87533083e3df4c2bae1ed12a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097027"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Zgoda administratora na platformę tożsamości firmy Microsoft

Niektóre uprawnienia wymagają zgody administratora, zanim będzie można go udzielić w ramach dzierżawy.  Możesz również użyć punktu końcowego zgody administratora, aby udzielić uprawnień dla całej dzierżawy.

## <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Podpisz użytkownika w aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora aplikacja musi mieć stronę lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może należeć do przepływu rejestracji aplikacji, części ustawień aplikacji lub być dedykowanym przepływem "Połącz". W wielu przypadkach ma sens, aby aplikacja pokazywała ten widok "Połącz" tylko po zalogowaniu się użytkownika przy użyciu konto Microsoft służbowego.

Po podpisaniu użytkownika do aplikacji można zidentyfikować organizację, do której należy administrator, przed zaproszeniem ich o zatwierdzenie wymaganych uprawnień. Chociaż nie jest to absolutnie konieczne, może pomóc w tworzeniu bardziej intuicyjnego środowiska dla użytkowników w organizacji. Aby podpisać użytkownika w programie, postępuj zgodnie z [samouczkami dotyczącymi protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe do zażądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora* platformy tożsamości firmy Microsoft.

```none
https://login.microsoftonline.com/{tenant}/v2.0/adminconsent
        ?client_id=6731de76-14a6-49ae-97bc-6eba6914391e
        &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
        &redirect_uri=http://localhost/myapp/permissions
        &state=12345
```

| Parametr | Warunek | Opis |
| :--- | :--- | :--- |
| `tenant` | Wymagane | Dzierżawa katalogu, z której chcesz zażądać uprawnień. Można podać w formacie identyfikatora GUID lub przyjaznej nazwy lub ogólnie przywoływane `organizations` , jak pokazano w przykładzie. Nie używaj elementu "Common", ponieważ konta osobiste nie mogą zapewnić zgody administratora, z wyjątkiem sytuacji, w której znajduje się dzierżawa. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawcami, użyj identyfikatora dzierżawy, o ile jest to możliwe. |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `redirect_uri` | Wymagane |Identyfikator URI przekierowania, w którym odpowiedź ma być wysyłana przez aplikację do obsługi. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu, aby kodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takie jak strona lub widok. |
|`scope` | Wymagane | Definiuje zestaw uprawnień wymaganych przez aplikację. Może to być statyczne (za pomocą `/.default` ) lub zakresy dynamiczne. Może to obejmować zakresy OIDC ( `openid` , `profile` , `email` ). |

W tym momencie usługa Azure AD wymaga od administratora dzierżawy zalogowania się w celu ukończenia żądania. Administrator jest proszony o zatwierdzenie wszystkich uprawnień żądanych w `scope` parametrze.  Jeśli użyto wartości statycznej ( `/.default` ), będzie ona działać podobnie jak punkt końcowy zgody administratora w wersji 1.0 i poprosić o zgodę na wszystkie zakresy Znalezione w wymaganych uprawnieniach (zarówno użytkownikowi, jak i aplikacji). Aby zażądać uprawnień aplikacji, należy użyć `/.default` wartości. Jeśli nie chcesz, aby administratorzy widzili odpowiednie uprawnienia na ekranie zgody administratora przez cały czas w czasie korzystania z programu `/.default` , najlepszym rozwiązaniem jest nie umieszczenie uprawnienia w sekcji wymagane uprawnienia. Zamiast tego można użyć zgody dynamicznej, aby dodać uprawnienia, które mają znajdować się na ekranie zgody w czasie wykonywania, zamiast korzystać z programu `/.default` .

### <a name="successful-response"></a>Pomyślna odpowiedź

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```none
http://localhost/myapp/permissions
    ?admin_consent=True
    &tenant=fa00d692-e9c7-4460-a743-29f2956fd429
    &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
    &state=12345
```

| Parametr | Opis |
| :--- | :--- |
| `tenant`| Dzierżawa katalogu, która udzieliła aplikacji żądane uprawnienia w formacie identyfikatora GUID.|
| `state` | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Ten stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok.|
| `scope` | Zestaw uprawnień, do których udzielono dostępu dla aplikacji.|
| `admin_consent` | Zostanie ustawiony na `True` .|

### <a name="error-response"></a>Odpowiedź na błąd

```none
http://localhost/myapp/permissions
        ?admin_consent=True
        &tenant=fa15d692-e9c7-4460-a743-29f2956fd429
        &error=consent_required
        &error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z
        &state=12345
```

Dodawanie do parametrów wyświetlanych w odpowiedzi na pomyślne, parametry błędu są obserwowane poniżej.

| Parametr | Opis |
|:-------------------|:-------------------------------------------------------------------------------------------------|
| `error` | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy.|
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu.|
| `tenant`| Dzierżawa katalogu, która udzieliła aplikacji żądane uprawnienia w formacie identyfikatora GUID.|
| `state` | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Ten stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok.|
| `admin_consent` | Zostanie ustawiona na wartość `True` wskazującą, że ta odpowiedź wystąpiła w przepływie zgody administratora.|

## <a name="next-steps"></a>Następne kroki
- Zobacz [, jak przekonwertować aplikację w celu korzystania z wielu dzierżawców](howto-convert-app-to-be-multi-tenant.md)
- Dowiedz się [, jak zgoda jest obsługiwana w warstwie protokołu OAuth 2,0 podczas przepływu przydzielenia kodu autoryzacji](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Dowiedz się, w [jaki sposób aplikacja wielodostępna może używać struktury zgody](./howto-convert-app-to-be-multi-tenant.md) do implementowania zgody "User" i "Administrator", która obsługuje bardziej zaawansowane wzorce aplikacji wielowarstwowej.
- Informacje o możliwościach [wyrażania zgody aplikacji usługi Azure AD](application-consent-experience.md)
