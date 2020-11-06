---
title: Azure Active Directory interfejs API REST — uwierzytelnianie
description: Używanie Azure Active Directory do uwierzytelniania w usłudze Azure App Configuration przy użyciu interfejsu API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424195"
---
# <a name="azure-active-directory-authentication"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Żądania HTTP mogą być uwierzytelniane przy użyciu schematu uwierzytelniania **okaziciela** z tokenem uzyskanym z Azure Active Directory (Azure AD). Te żądania muszą być przesyłane za pośrednictwem protokołu TLS.

## <a name="prerequisites"></a>Wymagania wstępne

Podmiot zabezpieczeń, który będzie używany do żądania tokenu usługi Azure AD, musi być przypisany do jednej z odpowiednich [ról konfiguracji aplikacji](./rest-api-authorization-azure-ad.md)

Podaj każde żądanie ze wszystkimi nagłówkami HTTP, które są wymagane do uwierzytelniania. Wymagane są następujące wymagania:

|  Nagłówek żądania | Opis  |
| --------------- | ------------ |
| **Autoryzacja** | Informacje o uwierzytelnianiu wymagane przez schemat **okaziciela** . Poniżej wyjaśniono format i szczegóły. |

**Przykład:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Azure Active Directory pozyskiwanie tokenów

Przed uzyskaniem tokenu usługi Azure AD musi on identyfikować użytkownika, który chce się uwierzytelnić, co odbiorca żąda tokenu oraz jaki punkt końcowy usługi Azure AD powinien używać.

### <a name="audience"></a>Grupy odbiorców

Token usługi Azure AD musi być żądany dla odpowiednich odbiorców. W przypadku konfiguracji aplikacji platformy Azure podczas żądania tokenu należy określić jedną z następujących grup odbiorców. Odbiorcy mogą być również nazywani "zasobem", dla którego żądano tokenu.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Gdy żądanymi odbiorcami jest {configurationStoreName}. azconfig. we/wy, musi dokładnie pasować do nagłówka żądania "host" (z uwzględnieniem wielkości liter) użytego do wysłania żądania.

### <a name="azure-ad-authority"></a>Urząd usługi Azure AD

Urząd usługi Azure AD jest punktem końcowym używanym do uzyskiwania tokenu usługi Azure AD. Ma postać `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segment odnosi się do identyfikatora dzierżawy Azure Active Directory, do którego należy użytkownik/aplikacja, która próbuje się uwierzytelnić.

### <a name="authentication-libraries"></a>Biblioteki uwierzytelniania

Platforma Azure udostępnia zestaw bibliotek o nazwie biblioteki uwierzytelniania Azure Active Directory (ADAL), aby uprościć proces uzyskiwania tokenu usługi Azure AD. Te biblioteki zostały skompilowane dla wielu języków. Dokumentację można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**błędy**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Przyczyna:** Nie podano nagłówka żądania autoryzacji z schematem okaziciela.
**Rozwiązanie:** Podaj prawidłowy ```Authorization``` nagłówek żądania http

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Przyczyna:** Token usługi Azure AD jest nieprawidłowy.
**Rozwiązanie:** Uzyskaj token usługi Azure AD z urzędu usługi Azure AD i upewnij się, że są używane odpowiednie odbiorcy.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Przyczyna:** Token usługi Azure AD jest nieprawidłowy.
**Rozwiązanie:** Uzyskaj token usługi Azure AD z urzędu usługi Azure AD i upewnij się, że dzierżawa usługi Azure AD jest skojarzona z subskrypcją, do której należy magazyn konfiguracji. Ten błąd może pojawić się, jeśli podmiot zabezpieczeń należy do więcej niż jednej dzierżawy usługi Azure AD.
