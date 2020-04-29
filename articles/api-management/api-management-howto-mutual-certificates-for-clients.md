---
title: Zabezpieczanie interfejsów API przy użyciu uwierzytelniania certyfikatów klientów w API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak zabezpieczyć dostęp do interfejsów API przy użyciu certyfikatów klienta
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76713134"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpieczać interfejsy API przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze API Management

API Management zapewnia możliwość zabezpieczenia dostępu do interfejsów API (np. klient API Management) przy użyciu certyfikatów klienta. Można sprawdzić poprawność certyfikatu przychodzącego i sprawdzić właściwości certyfikatu przed żądanymi wartościami przy użyciu wyrażeń zasad.

Aby uzyskać informacje o zabezpieczaniu dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta (tj. API Management zaplecza), zobacz [jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Aby otrzymywać i weryfikować certyfikaty klienta za pośrednictwem protokołu HTTP/2 w warstwach Deweloper, podstawowa, standardowa lub Premium, należy włączyć ustawienie "Negocjuj certyfikat klienta" w bloku "domeny niestandardowe", jak pokazano poniżej.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Aby otrzymywać i weryfikować certyfikaty klienta w warstwie zużycia, należy włączyć ustawienie "Żądaj certyfikatu klienta" w bloku "domeny niestandardowe", jak pokazano poniżej.

![Żądaj certyfikatu klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Sprawdzanie wystawcy i tematu

Poniższe zasady można skonfigurować w celu sprawdzenia wystawcy i tematu certyfikatu klienta:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów zamiast. `context.Request.Certificate.Verify()`
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisku palca

Poniższe zasady można skonfigurować w celu sprawdzenia odcisku palca certyfikatu klienta:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów zamiast. `context.Request.Certificate.Verify()`
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisku palca w odniesieniu do certyfikatów przekazanych do API Management

Poniższy przykład pokazuje, jak sprawdzić odcisk palca certyfikatu klienta względem certyfikatów przekazanych do API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów zamiast. `context.Request.Certificate.Verify()`
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

> [!TIP]
> Problem zakleszczenia certyfikatu klienta opisany w tym [artykule](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) może być manifestem na kilka sposobów, np. żądania zawieszania `403 Forbidden` , żądania powodują powstanie kodu stanu `context.Request.Certificate` po `null`upływie limitu czasu. Ten problem ma zwykle `POST` wpływ `PUT` na żądania z zawartością o długości około 60KB lub większą.
> Aby zapobiec wystąpieniu tego problemu, Włącz ustawienie "Negocjuj certyfikat klienta" dla żądanych nazw hostów w bloku "domeny niestandardowe", jak pokazano poniżej. Ta funkcja nie jest dostępna w warstwie zużycia.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Następne kroki

-   [Jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Jak przekazać certyfikaty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
