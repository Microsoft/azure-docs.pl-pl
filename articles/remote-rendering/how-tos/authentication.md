---
title: Authentication
description: Wyjaśnia, jak działa uwierzytelnianie
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: dc325fdf68c5afbb122f9e77c5509a6a8053a12e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427455"
---
# <a name="configure-authentication"></a>Konfigurowanie uwierzytelniania

Zdalne renderowanie na platformie Azure używa tego samego mechanizmu uwierzytelniania jak [kotwice przestrzenne platformy Azure](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Klienci muszą ustawić *jedną* z następujących opcji, aby pomyślnie wywołać interfejsy API REST:

* **AccountKey**: można uzyskać na karcie "klucze" dla konta renderowania zdalnego na Azure Portal. Klucze kont są zalecane tylko w przypadku projektowania/tworzenia prototypów.
    ![Identyfikator konta](./media/azure-account-primary-key.png)

* **AuthenticationToken**: jest tokenem usługi Azure AD, który można uzyskać za pomocą [biblioteki MSAL](../../active-directory/develop/msal-overview.md). Istnieje wiele różnych przepływów umożliwiających zaakceptowanie poświadczeń użytkownika i użycie tych poświadczeń w celu uzyskania tokenu dostępu.

* **MRAccessToken**: jest tokenem Mr, który można uzyskać z usługi Azure Mixed Reality Security Token Service (STS). Pobrane z `https://sts.mixedreality.azure.com` punktu końcowego przy użyciu wywołania REST podobnego do poniższego wywołania:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Gdzie nagłówek autoryzacji jest sformatowany w następujący sposób: `Bearer <Azure_AD_token>` lub `Bearer <accoundId>:<accountKey>` . Dawniej jest preferowany dla bezpieczeństwa. Token zwrócony z tego wywołania REST to token dostępu MR.

## <a name="authentication-for-deployed-applications"></a>Uwierzytelnianie wdrożonych aplikacji

Klucze kont są zalecane do szybkiego tworzenia prototypów, tylko podczas programowania. Zaleca się, aby nie dostarczać aplikacji do środowiska produkcyjnego przy użyciu osadzonego klucza konta. Zalecanym podejściem jest użycie podejścia uwierzytelniania usługi Azure AD opartego na użytkowniku lub usłudze.

 Uwierzytelnianie usługi Azure AD jest opisane w sekcji [uwierzytelnianie użytkowników usługi Azure AD](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) w usłudze [Azure przestrzenny kotwics (ASA)](../../spatial-anchors/index.yml) .

 Aby uzyskać więcej informacji, zobacz [Samouczek: Zabezpieczanie zdalnego renderowania i magazynu modelu na platformie Azure — uwierzytelnianie Azure Active Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Aby ułatwić kontrolę poziomu dostępu udzielonego usłudze, podczas udzielania dostępu opartego na rolach należy stosować następujące role:

* **Administrator zdalnego renderowania**: umożliwia użytkownikowi konwertowanie funkcji konwersji, sesji zarządzania, renderowania i diagnostyki na potrzeby renderowania zdalnego na platformie Azure.
* **Klient renderowania zdalnego**: umożliwia użytkownikowi zarządzanie sesjami, renderowaniem i diagnostyką w przypadku renderowania zdalnego na platformie Azure.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta](create-an-account.md)
* [Używanie interfejsów API frontonu platformy Azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)