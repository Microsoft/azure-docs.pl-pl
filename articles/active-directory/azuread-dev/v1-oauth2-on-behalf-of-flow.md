---
title: Uwierzytelnianie między usługami z użyciem usługi OAuth 2.0 w imieniu przepływu | Microsoft Docs
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu zaimplementowania uwierzytelniania między usługami w ramach przepływu OAuth 2.0 w imieniu użytkownika.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 08/5/2020
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f746cc654934464d907c6ad669eb7470e4dcaeeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117740"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Wywołania między usługami, które korzystają z delegowanej tożsamości użytkownika w ramach przepływu w imieniu

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Przepływ OAuth 2,0 w imieniu (OBO) umożliwia aplikacji, która wywołuje usługę lub interfejs API sieci Web, aby przekazywać uwierzytelnianie użytkowników do innej usługi lub internetowego interfejsu API. Przepływ OBO propaguje delegowaną tożsamość użytkownika i uprawnienia za pomocą łańcucha żądań. W przypadku usługi warstwy środkowej, aby żądania były uwierzytelniane do usługi podrzędnej, musi on zabezpieczyć token dostępu z Azure Active Directory (Azure AD) w imieniu użytkownika.

> [!IMPORTANT]
> Od maja 2018 `id_token` nie można używać w przepływie w imieniu użytkownika.  Aplikacje jednostronicowe (aplikacji jednostronicowych) muszą przekazać token dostępu do klienta poufnego w warstwie środkowej w celu przeprowadzenia przepływów OBO. Aby uzyskać więcej szczegółowych informacji na temat klientów, którzy mogą wykonywać wywołania w imieniu, zobacz [ograniczenia](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagram przepływu w imieniu

Przepływ OBO jest uruchamiany po uwierzytelnieniu użytkownika w aplikacji, która używa [przepływu przydzielenia kodu autoryzacji OAuth 2,0](v1-protocols-oauth-code.md). W tym momencie aplikacja wysyła token dostępu (token A) do interfejsu API sieci Web warstwy środkowej (API A) zawierającego oświadczenia użytkownika i wyraża zgodę na dostęp do interfejsu API A. Następnie interfejs API A wysyła uwierzytelnione żądanie do podrzędnego interfejsu API sieci Web (API B).

Te kroki stanowią "w imieniu" przepływu: ![ pokazuje kroki w przepływie protokołu OAuth 2.0 w imieniu użytkownika](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka wysyła żądanie do interfejsu API A przy użyciu tokenu A.
1. Interfejs API A uwierzytelnia się w punkcie końcowym wystawiania tokenów usługi Azure AD i żąda tokenu dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenów usługi Azure AD sprawdza poprawność poświadczeń interfejsu API A i wystawia token dostępu dla interfejsu API B (token B).
1. Żądanie do interfejsu API B zawiera token B w nagłówku autoryzacji.
1. Interfejs API B zwraca dane z zabezpieczonego zasobu.

>[!NOTE]
>W tokenie dostępu używanym do żądania tokenu dla usługi podrzędnej musi być identyfikator usługi wysyłającej żądanie OBO. Token musi być również podpisany przy użyciu globalnego klucza podpisywania Azure Active Directory (co jest ustawieniem domyślnym dla aplikacji zarejestrowanych za pośrednictwem **rejestracje aplikacji** w portalu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Rejestrowanie aplikacji i usługi w usłudze Azure AD

Zarejestruj zarówno usługę warstwy środkowej, jak i aplikację kliencką w usłudze Azure AD.

### <a name="register-the-middle-tier-service"></a>Rejestrowanie usługi warstwy środkowej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i przejrzyj listę **katalogów** , aby wybrać dzierżawę Active Directory dla aplikacji.
1. W lewym okienku wybierz pozycję **więcej usług** , a następnie wybierz pozycję **Azure Active Directory**.
1. Wybierz **rejestracje aplikacji** a następnie pozycję **Nowa rejestracja**.
1. Wprowadź przyjazną nazwę aplikacji i wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania na podstawowy adres URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. W Azure Portal wybierz aplikację, a następnie wybierz pozycję **certyfikaty & wpisy tajne**.
1. Wybierz pozycję **nowy klucz tajny klienta** i Dodaj klucz tajny o czasie trwania jednego roku lub dwóch lat.
1. Po zapisaniu tej strony Azure Portal zostanie wyświetlona wartość klucza tajnego. Skopiuj i Zapisz wartość klucza tajnego w bezpiecznej lokalizacji.
1. Utwórz zakres aplikacji na stronie **Uwidacznianie interfejsu API** dla aplikacji, a następnie kliknij pozycję "Dodaj zakres".  Portal może wymagać również utworzenia identyfikatora URI aplikacji. 

> [!IMPORTANT]
> Klucz tajny jest wymagany do skonfigurowania ustawień aplikacji w implementacji. Ta wartość klucza tajnego nie jest ponownie wyświetlana i nie można jej pobrać w żaden inny sposób. Zapisz go, gdy tylko będzie widoczny w Azure Portal.

### <a name="register-the-client-application"></a>Rejestrowanie aplikacji klienckiej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto i przejrzyj listę **katalogów** , aby wybrać dzierżawę Active Directory dla aplikacji.
1. W lewym okienku wybierz pozycję **więcej usług** , a następnie wybierz pozycję **Azure Active Directory**.
1. Wybierz **rejestracje aplikacji** a następnie pozycję **Nowa rejestracja**.
1. Wprowadź przyjazną nazwę aplikacji i wybierz typ aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. Ustaw identyfikator URI przekierowania na podstawowy adres URL.
1. Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Skonfiguruj uprawnienia dla aplikacji. W obszarze **uprawnienia interfejsu API**wybierz pozycję **Dodaj uprawnienie** , a następnie **Moje interfejsy API**.
1. W polu tekstowym wpisz nazwę usługi warstwy środkowej.
1. Wybierz **pozycję Wybierz uprawnienia** , a następnie wybierz zakres utworzony w ostatnim kroku rejestracji warstwy środkowej.

### <a name="configure-known-client-applications"></a>Skonfiguruj znane aplikacje klienckie

W tym scenariuszu Usługa warstwy środkowej musi uzyskać zgodę użytkownika na dostęp do podrzędnego interfejsu API bez interakcji z użytkownikiem. Opcja udzielenia dostępu do podrzędnego interfejsu API musi zostać przedstawiona jako część kroku wyrażania zgody podczas uwierzytelniania.

Wykonaj poniższe kroki, aby jawnie powiązać rejestrację aplikacji klienta w usłudze Azure AD z rejestracją usługi warstwy środkowej. Ta operacja Scala zgodę wymaganą przez klienta i warstwę środkową w pojedynczym oknie dialogowym.

1. Przejdź do rejestracji usługi warstwy środkowej i wybierz pozycję **manifest** , aby otworzyć Edytor manifestu.
1. Znajdź `knownClientApplications` Właściwość Array i Dodaj identyfikator klienta aplikacji klienckiej jako element.
1. Zapisz manifest, wybierając pozycję **Zapisz**.

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu między usługami

Aby zażądać tokenu dostępu, wprowadź polecenie HTTP w punkcie końcowym usługi Azure AD specyficznym dla dzierżawy o następujących parametrach:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Aplikacja kliencka jest zabezpieczona przez wspólny klucz tajny lub certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego

Gdy jest używany wspólny klucz tajny, żądanie tokenu dostępu między usługami zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. Żądanie OBO używa tokenu sieci Web JSON (JWT), dlatego musi mieć wartość **urn: IETF: params: OAuth: Grant-Type: JWT**. |
| twierdz |wymagane | Wartość tokenu dostępu używanego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w Azure Portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |wymagane | Klucz zarejestrowany dla usługi wywołującej w usłudze Azure AD. Ta wartość powinna zostać zanotowana w chwili rejestracji. |
| zasób |wymagane | Identyfikator URI identyfikatora aplikacji usługi odbiorczej (zabezpieczony zasób). Aby znaleźć identyfikator URI aplikacji w Azure Portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W imieniu przepływu wartość musi być **on_behalf_of**. |
| scope |wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. W przypadku połączenia OpenID Connect należy określić zakres **OpenID Connect** .|

#### <a name="example"></a>Przykład

Następujące HTTP POST żąda tokenu dostępu dla internetowego https://graph.microsoft.com interfejsu API. `client_id`Identyfikuje usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

Żądanie tokenu dostępu między usługami i certyfikatem zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. Żądanie OBO używa tokenu dostępu JWT, dlatego musi mieć wartość **urn: IETF: params: OAuth: Grant-Type: JWT**. |
| twierdz |wymagane | Wartość tokenu użytego w żądaniu. |
| client_id |wymagane | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w Azure Portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_assertion_type |wymagane |Wartość musi być równa `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | Token sieci Web JSON, który można utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Aby dowiedzieć się więcej o formacie potwierdzenia i o sposobie rejestrowania certyfikatu, zobacz  [poświadczenia certyfikatu](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) .|
| zasób |wymagane | Identyfikator URI identyfikatora aplikacji usługi odbiorczej (zabezpieczony zasób). Aby znaleźć identyfikator URI aplikacji w Azure Portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W imieniu przepływu wartość musi być **on_behalf_of**. |
| scope |wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. W przypadku połączenia OpenID Connect należy określić zakres **OpenID Connect** .|

Te parametry są prawie takie same, jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że `client_secret parameter` jest zastępowany przez dwa parametry: `client_assertion_type` i `client_assertion` .

#### <a name="example"></a>Przykład

Następujące HTTP POST żąda tokenu dostępu dla internetowego https://graph.microsoft.com interfejsu API z certyfikatem. `client_id`Identyfikuje usługę, która żąda tokenu dostępu.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Odpowiedź na token dostępu z usługi do usługi

Odpowiedź sukcesu to odpowiedź OAuth 2,0 JSON z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD **jest znak**. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu udzielony w tokenie. |
| expires_in |Czas ważności tokenu dostępu (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. Ta wartość służy do określenia okresu istnienia buforowanych tokenów. |
| zasób |Identyfikator URI identyfikatora aplikacji usługi odbiorczej (zabezpieczony zasób). |
| access_token |Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbiorczej. |
| id_token |Żądany token identyfikatora. Usługa wywołująca może użyć tego tokenu do zweryfikowania tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. |
| refresh_token |Token odświeżania dla żądanego tokenu dostępu. Usługa wywołująca może używać tego tokenu do żądania innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. |

### <a name="success-response-example"></a>Przykład odpowiedzi sukcesu

Poniższy przykład przedstawia Pomyślne odpowiedzi na żądanie tokenu dostępu dla internetowego https://graph.microsoft.com interfejsu API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

Punkt końcowy tokenu usługi Azure AD zwraca odpowiedź na błąd, gdy próbuje uzyskać token dostępu dla podrzędnego interfejsu API, który jest ustawiony za pomocą zasad dostępu warunkowego (na przykład uwierzytelnianie wieloskładnikowe). Usługa warstwy środkowej powinna wystawić ten błąd w aplikacji klienckiej, dzięki czemu aplikacja kliencka może zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Korzystanie z tokenu dostępu w celu uzyskania dostępu do zabezpieczonego zasobu

Usługa warstwy środkowej może korzystać z tokenu uzyskanego dostępu, aby uwierzytelniać żądania do podrzędnego interfejsu API sieci Web przez ustawienie tokenu w `Authorization` nagłówku.

### <a name="example"></a>Przykład

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Potwierdzenia SAML uzyskane przy użyciu przepływu OBO protokołu OAuth 2.0

Niektóre usługi sieci Web oparte na protokole OAuth muszą uzyskiwać dostęp do innych interfejsów API usługi sieci Web, które akceptują potwierdzenia SAML w nieinteraktywnych przepływach. Azure Active Directory może zapewnić potwierdzenie SAML w odpowiedzi na w imieniu przepływu, który używa usługi sieci Web opartej na protokole SAML jako zasobu docelowego.

>[!NOTE]
>Jest to niestandardowe rozszerzenie dla przepływu OAuth 2,0 w imieniu użytkownika, które umożliwia aplikacji opartej na OAuth2 dostęp do punktów końcowych interfejsu API usługi sieci Web, które używają tokenów SAML.

> [!TIP]
> Po wywołaniu usługi sieci Web chronionej przy użyciu protokołu SAML z aplikacji sieci Web frontonu można po prostu wywołać interfejs API i zainicjować normalny przepływ uwierzytelniania interaktywnego za pomocą istniejącej sesji użytkownika. Należy używać przepływu OBO tylko wtedy, gdy wywołanie usługi Service to Service wymaga tokenu języka SAML do zapewnienia kontekstu użytkownika.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Uzyskiwanie tokenu SAML przy użyciu żądania OBO ze wspólnym kluczem tajnym

Żądanie Service-Service dla potwierdzenia SAML zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane | Typ żądania tokenu. Dla żądania, które używa tokenu JWT, wartość musi być **urn: IETF: params: OAuth: Grant-Type: JWT**. |
| twierdz |wymagane | Wartość tokenu dostępu używanego w żądaniu.|
| client_id |wymagane | Identyfikator aplikacji przypisany do usługi wywołującej podczas rejestracji w usłudze Azure AD. Aby znaleźć identyfikator aplikacji w Azure Portal, wybierz pozycję **Active Directory**, wybierz katalog, a następnie wybierz nazwę aplikacji. |
| client_secret |wymagane | Klucz zarejestrowany dla usługi wywołującej w usłudze Azure AD. Ta wartość powinna zostać zanotowana w chwili rejestracji. |
| zasób |wymagane | Identyfikator URI identyfikatora aplikacji usługi odbiorczej (zabezpieczony zasób). Jest to zasób, który będzie odbiorcami tokenu SAML. Aby znaleźć identyfikator URI aplikacji w Azure Portal, wybierz pozycję **Active Directory** i wybierz katalog. Wybierz nazwę aplikacji, wybierz pozycję **wszystkie ustawienia**, a następnie wybierz pozycję **Właściwości**. |
| requested_token_use |wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W imieniu przepływu wartość musi być **on_behalf_of**. |
| requested_token_type | wymagane | Określa typ żądanego tokenu. Wartość może być **urn: IETF: params: OAuth: token-Type: SAML2** lub **urn: IETF: params: OAuth: token-Type: saml1** , w zależności od wymagań zasobu, do którego uzyskiwany jest dostęp. |

Odpowiedź zawiera token języka SAML zakodowany w formacie UTF8 i Base64url.

- **Danych SubjectConfirmationData dla potwierdzenia SAML pochodzącego z wywołania OBO**: Jeśli aplikacja docelowa wymaga wartości odbiorcy w **danych SubjectConfirmationData**, wartość musi być adresem URL odpowiedzi innego niż symbol wieloznaczny w konfiguracji aplikacji zasobów.
- **Węzeł danych SubjectConfirmationData**: węzeł nie może zawierać atrybutu **InResponseTo** , ponieważ nie jest częścią odpowiedzi SAML. Aplikacja, która otrzymała token SAML, musi być w stanie zaakceptować potwierdzenie SAML bez atrybutu **InResponseTo** .

- **Wyrażanie zgody**: należy przyznać zgodę na otrzymywanie tokenu SAML zawierającego dane użytkownika w przepływie protokołu OAuth. Aby uzyskać informacje na temat uprawnień i uzyskiwania zgody administratora, zobacz [uprawnienia i zgoda w punkcie końcowym Azure Active Directory v 1.0](./v1-permissions-consent.md).

### <a name="response-with-saml-assertion"></a>Odpowiedź z potwierdzeniem SAML

| Parametr | Opis |
| --- | --- |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD **jest znak**. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Zakres dostępu udzielony w tokenie. |
| expires_in |Czas ważności tokenu dostępu (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. Ta wartość służy do określenia okresu istnienia buforowanych tokenów. |
| zasób |Identyfikator URI identyfikatora aplikacji usługi odbiorczej (zabezpieczony zasób). |
| access_token |Parametr, który zwraca potwierdzenie SAML. |
| refresh_token |Token odświeżania. Usługa wywołująca może używać tego tokenu do żądania innego tokenu dostępu po wygaśnięciu bieżącego potwierdzenia SAML. |

- token_type: Okazicielaer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- zasoby `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn: IETF: params: OAuth: token-Type: SAML2
- refresh_token: \<Refresh token\>

## <a name="client-limitations"></a>Ograniczenia klienta

Klienci publiczni z adresami URL odpowiedzi z symbolami wieloznacznymi nie mogą używać `id_token` dla przepływów OBO. Jednak poufny klient może nadal korzystać z tokenów **dostępu** uzyskanych za pomocą przepływu niejawnego, nawet jeśli klient publiczny ma zarejestrowany identyfikator URI przekierowania z symbolem wieloznacznym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o protokole OAuth 2,0 i innym sposobie przeprowadzania uwierzytelniania między usługami, które używa poświadczeń klienta:

* [Uwierzytelnianie usługi przy użyciu poświadczeń klienta uwierzytelniania OAuth 2,0 w usłudze Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [Uwierzytelnianie OAuth 2,0 w usłudze Azure AD](v1-protocols-oauth-code.md)