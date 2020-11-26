---
title: Azure Active Directory interfejs API REST — uwierzytelnianie
description: Używanie Azure Active Directory do uwierzytelniania w usłudze Azure App Configuration przy użyciu interfejsu API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 73d951b3e85c170cf068693bb9b374349516ca8f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183485"
---
# <a name="azure-active-directory-authentication"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Żądania HTTP można uwierzytelniać przy użyciu `Bearer` schematu uwierzytelniania z tokenem uzyskanym z Azure Active Directory (Azure AD). Należy przesłać te żądania za pośrednictwem Transport Layer Security (TLS).

## <a name="prerequisites"></a>Wymagania wstępne

Należy przypisać podmiot zabezpieczeń, który jest używany do żądania tokenu usługi Azure AD do jednej z odpowiednich [ról konfiguracji aplikacji platformy Azure](./rest-api-authorization-azure-ad.md).

Podaj każde żądanie ze wszystkimi nagłówkami HTTP, które są wymagane do uwierzytelniania. Poniżej przedstawiono minimalne wymagania:

|  Nagłówek żądania | Opis  |
| --------------- | ------------ |
| `Authorization` | Informacje o uwierzytelnianiu wymagane przez `Bearer` schemat. |

**Przykład:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Pozyskiwanie tokenów usługi Azure AD

Przed uzyskaniem tokenu usługi Azure AD należy określić użytkownika, który ma być uwierzytelniany, jak odbiorców, dla których żądasz tokenu, oraz do czego służy punkt końcowy usługi Azure AD.

### <a name="audience"></a>Grupy odbiorców

Zażądaj tokenu usługi Azure AD za pomocą odpowiednich odbiorców. W przypadku konfiguracji aplikacji platformy Azure Użyj jednej z następujących grup odbiorców. Odbiorcy mogą również być określeni jako *zasób* , dla którego żądano tokenu.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Gdy zażądano odbiorców `{configurationStoreName}.azconfig.io` , musi on dokładnie pasować do `Host` nagłówka żądania (z uwzględnieniem wielkości liter) użytego do wysłania żądania.

### <a name="azure-ad-authority"></a>Urząd usługi Azure AD

Urząd usługi Azure AD jest punktem końcowym używanym do uzyskiwania tokenu usługi Azure AD. Ma postać `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segment odwołuje się do identyfikatora dzierżawy usługi Azure AD, do którego należy użytkownik lub aplikacja próbująca się uwierzytelnić.

### <a name="authentication-libraries"></a>Biblioteki uwierzytelniania

Platforma Azure udostępnia zestaw bibliotek o nazwie Azure Active Directory bibliotek uwierzytelniania, co upraszcza proces uzyskiwania tokenu usługi Azure AD. Platforma Azure tworzy te biblioteki dla wielu języków. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>błędy

Mogą wystąpić następujące błędy.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Przyczyna:** Nie podano nagłówka żądania autoryzacji z `Bearer` schematem.

**Rozwiązanie:** Podaj prawidłowy `Authorization` nagłówek żądania HTTP.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Przyczyna:** Token usługi Azure AD jest nieprawidłowy.

**Rozwiązanie:** Uzyskaj token usługi Azure AD z urzędu usługi Azure AD, a następnie upewnij się, że użyto odpowiednich odbiorców.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Przyczyna:** Token usługi Azure AD jest nieprawidłowy.

**Rozwiązanie:** Uzyskaj token usługi Azure AD z urzędu usługi Azure AD. Upewnij się, że dzierżawa usługi Azure AD jest skojarzona z subskrypcją, do której należy magazyn konfiguracji. Ten błąd może pojawić się, jeśli podmiot zabezpieczeń należy do więcej niż jednej dzierżawy usługi Azure AD.