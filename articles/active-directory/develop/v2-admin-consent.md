---
title: Protokoły zgody administratora platformy tożsamości firmy Microsoft
description: Opis autoryzacji w punkcie końcowym platformy tożsamości firmy Microsoft, w tym zakresy, uprawnienia i zgody.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 537d609c1281929203d1891f37614b7627e1683a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868665"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Zgoda administratora na platformie tożsamości firmy Microsoft

Niektóre uprawnienia wymagają zgody administratora, zanim będą mogły zostać udzielone w ramach dzierżawy.  Można również użyć punktu końcowego zgody administratora, aby udzielić uprawnień całej dzierżawie.

## <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Zaloguj użytkownika do aplikacji

Zazwyczaj podczas tworzenia aplikacji, która używa punktu końcowego zgody administratora, aplikacja potrzebuje strony lub widoku, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być częścią przepływu rejestracji aplikacji, częścią ustawień aplikacji lub może być dedykowanym przepływem "connect". W wielu przypadkach ma sens, aby aplikacja wyświetlała ten widok "połącz" tylko wtedy, gdy użytkownik zalogował się za pomocą służbowego konta Microsoft.

Po zalogowaniu użytkownika do aplikacji można zidentyfikować organizację, do której należy administrator, zanim poprosisz go o zatwierdzenie niezbędnych uprawnień. Chociaż nie jest to absolutnie konieczne, może pomóc w utworzeniu bardziej intuicyjnego środowiska dla użytkowników organizacji. Aby zalogować się do użytkownika, postępuj zgodnie z naszymi [samouczkami protokołu platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy będziesz gotowy do żądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora*platformy tożsamości firmy Microsoft .

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametr     | Warunek     | Opis                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Wymagany | Dzierżawy katalogu, które chcesz zażądać uprawnień od. Może być dostarczony w guid lub przyjazny format `organizations` nazwy lub ogólnie odwołuje się do jak widać w przykładzie. Nie należy używać "wspólne", ponieważ konta osobiste nie może udzielić zgody administratora, chyba że w kontekście dzierżawy. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawcami, użyj identyfikatora dzierżawy, jeśli to możliwe. |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `redirect_uri` | Wymagany |Identyfikator URI przekierowania, w którym ma zostać wysłana odpowiedź dla aplikacji do obsługi. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania, które zostały zarejestrowane w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu, aby zakodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takie jak strona lub widok, na których się znajdowały. |
|`scope`        | Wymagany      | Definiuje zestaw uprawnień wymaganych przez aplikację. Może to być statyczne (przy użyciu /.default) lub zakresy dynamiczne.  Może to obejmować zakresy OIDC (`openid`, , `profile` `email`). |


W tym momencie usługa Azure AD wymaga administratora dzierżawy, aby zalogować się, aby zakończyć żądanie. Administrator jest proszony o zatwierdzenie wszystkich uprawnień wymaganych `scope` w parametrze.  Jeśli użyto wartości statycznej`/.default`( ) będzie działać jak punkt końcowy zgody administratora w wersji 1.0 i zażądać zgody dla wszystkich zakresów znalezionych w wymaganych uprawnieniach dla aplikacji.

### <a name="successful-response"></a>Skuteczna reakcja

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parametr         | Opis                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Dzierżawy katalogu, który przyznał aplikacji uprawnienia, o które żądano, w formacie GUID.|
| `state`           | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały.|
| `scope`          | Zestaw uprawnień, którym udzielono dostępu dla aplikacji.|
| `admin_consent`   | Zostanie ustawiona `True`na .|

### <a name="error-response"></a>Odpowiedź na błąd

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Dodając do parametrów widocznych w pomyślnej odpowiedzi, parametry błędu są widoczne poniżej.

| Parametr          | Opis                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy.|
| `error_description`| Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu.|
| `tenant`| Dzierżawy katalogu, który przyznał aplikacji uprawnienia, o które żądano, w formacie GUID.|
| `state`           | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały.|
| `admin_consent`   | Zostanie ustawiona, aby `True` wskazać, że ta odpowiedź wystąpiła w przepływie zgody administratora.|

## <a name="next-steps"></a>Następne kroki
- Zobacz, [jak konwertować aplikację na wielodostępną](howto-convert-app-to-be-multi-tenant.md)
- Dowiedz się, jak [zgoda jest obsługiwana w warstwie protokołu OAuth 2.0 podczas przepływu udzielania kodu autoryzacji.](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- Dowiedz [się, jak aplikacja z wieloma dzierżawami może używać struktury zgody](active-directory-devhowto-multi-tenant-overview.md) do implementowania zgody "użytkownik" i "administrator", obsługując bardziej zaawansowane wzorce aplikacji wielowarstwowych.
- Opis [środowiska zgody aplikacji usługi Azure AD](application-consent-experience.md)
