---
title: Uwierzytelnianie usługi Azure AD przy użyciu protokołu OAuth 2.0 | Microsoft Docs
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu zaimplementowania uwierzytelniania usługi przy użyciu poświadczeń klienta OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85551711"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Wywołania usługi do obsługi przy użyciu poświadczeń klienta (wspólny klucz tajny lub certyfikat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Przepływ przydzielenia poświadczeń klienta OAuth 2,0 umożliwia usłudze sieci Web (*poufnego klienta*) użycie własnych poświadczeń zamiast personifikowania użytkownika w celu uwierzytelniania podczas wywoływania innej usługi sieci Web. W tym scenariuszu klient jest zwykle usługą sieci Web warstwy środkowej, usługą demona lub witryną sieci Web. W przypadku wyższego poziomu pewności usługa Azure AD umożliwia usłudze wywołującej używanie certyfikatu (zamiast wspólnego klucza tajnego) jako poświadczenia.

## <a name="client-credentials-grant-flow-diagram"></a>Diagram przepływu dotacji dla poświadczeń klienta
Na poniższym diagramie wyjaśniono, w jaki sposób usługa przepływ poświadczeń klienta działa w Azure Active Directory (Azure AD).

![Przepływ przyznania poświadczeń klienta OAuth 2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Aplikacja kliencka jest uwierzytelniana w punkcie końcowym wystawiania tokenów usługi Azure AD i żąda tokenu dostępu.
2. Punkt końcowy wystawiania tokenów usługi Azure AD wystawia token dostępu.
3. Token dostępu jest używany do uwierzytelniania w zabezpieczonym zasobie.
4. Dane z zabezpieczonego zasobu są zwracane do aplikacji klienckiej.

## <a name="register-the-services-in-azure-ad"></a>Rejestrowanie usług w usłudze Azure AD
Zarejestruj usługę wywołującą i usługę otrzymującą w Azure Active Directory (Azure AD). Aby uzyskać szczegółowe instrukcje, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Żądanie tokenu dostępu
Aby zażądać tokenu dostępu, użyj wpisu HTTP POST w punkcie końcowym usługi Azure AD specyficznym dla dzierżawy.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu między usługami
Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka zostanie zabezpieczona przez wspólny klucz tajny, czy certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego
Gdy jest używany wspólny klucz tajny, żądanie tokenu dostępu między usługami zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane |Określa typ żądanego przydziału. W przepływie przydzielenia poświadczeń klienta wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD wywołującej usługę sieci Web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [Azure Portal](https://portal.azure.com)kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, kliknij aplikację. Client_id jest *Identyfikator aplikacji* |
| client_secret |wymagane |Wprowadź klucz zarejestrowany dla wywołującej usługi sieci Web lub aplikacji demona w usłudze Azure AD. Aby utworzyć klucz, w Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, kliknij aplikację, kliknij pozycję **Ustawienia**, kliknij pozycję **klucze** i Dodaj klucz.  URL — Koduj ten klucz tajny podczas jego udostępniania. |
| zasób |wymagane |Wprowadź identyfikator URI aplikacji dla usługi sieci Web otrzymującej. Aby znaleźć identyfikator URI aplikacji, w Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, kliknij aplikację usługi, a następnie kliknij pozycję **Ustawienia** i **Właściwości**. |

#### <a name="example"></a>Przykład
Poniższe żądanie HTTP POST żąda [tokenu dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) do `https://service.contoso.com/` usługi sieci Web. `client_id`Identyfikuje usługę sieci Web, która żąda tokenu dostępu.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem
Żądanie tokenu dostępu między usługami i certyfikatem zawiera następujące parametry:

| Parametr | Typ | Opis |
| --- | --- | --- |
| grant_type |wymagane |Określa żądany typ odpowiedzi. W przepływie przydzielenia poświadczeń klienta wartość musi być **client_credentials**. |
| client_id |wymagane |Określa identyfikator klienta usługi Azure AD wywołującej usługę sieci Web. Aby znaleźć identyfikator klienta aplikacji wywołującej, w [Azure Portal](https://portal.azure.com)kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, kliknij aplikację. Client_id jest *Identyfikator aplikacji* |
| client_assertion_type |wymagane |Wartość musi być równa `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | Potwierdzenie (token sieci Web JSON), które należy utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Przeczytaj informacje o [poświadczeniach certyfikatów](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) , aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia.|
| zasób | wymagane |Wprowadź identyfikator URI aplikacji dla usługi sieci Web otrzymującej. Aby znaleźć identyfikator URI aplikacji, w Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, kliknij aplikację usługi, a następnie kliknij pozycję **Ustawienia** i **Właściwości**. |

Zwróć uwagę, że parametry są prawie takie same, jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że parametr client_secret jest zastępowany przez dwa parametry: client_assertion_type i client_assertion.

#### <a name="example"></a>Przykład
Następujące HTTP POST żąda tokenu dostępu dla `https://service.contoso.com/` usługi sieci Web z certyfikatem. `client_id`Identyfikuje usługę sieci Web, która żąda tokenu dostępu.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Odpowiedź na token dostępu z usługi do usługi

Odpowiedź dotycząca sukcesu zawiera odpowiedź OAuth 2,0 JSON z następującymi parametrami:

| Parametr | Opis |
| --- | --- |
| access_token |Żądany token dostępu. Wywoływana usługa sieci Web może używać tego tokenu do uwierzytelniania w usłudze sieci Web otrzymującej. |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD **jest znak**. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. Ta wartość służy do określenia okresu istnienia buforowanych tokenów. |
| not_before |Czas, od którego token dostępu będzie bezużyteczny. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu ważności dla tokenu.|
| zasób |Identyfikator URI identyfikatora aplikacji dla usługi sieci Web odbiorczej. |

#### <a name="example-of-response"></a>Przykład odpowiedzi
Poniższy przykład przedstawia Pomyślne odpowiedzi na żądanie tokenu dostępu do usługi sieci Web.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>Korzystanie z tokenu dostępu w celu uzyskania dostępu do zabezpieczonego zasobu

Usługa może korzystać z tokenu uzyskanego dostępu, aby wykonywać uwierzytelnione żądania do podrzędnego interfejsu API sieci Web przez ustawienie tokenu w `Authorization` nagłówku.

### <a name="example"></a>Przykład

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Zobacz także
* [Uwierzytelnianie OAuth 2,0 w usłudze Azure AD](v1-protocols-oauth-code.md)
* [Przykład w języku c# usługi do obsługi połączeń z wspólnym kluczem tajnym](https://github.com/Azure-Samples/active-directory-dotnet-daemon) i [przykładem w języku c# wywołania usługi do obsługi z certyfikatem](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
