---
title: Publiczne i poufne aplikacje klienckie (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Informacje na temat klienta publicznego i poufnych aplikacji klienckich w bibliotece uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063760"
---
# <a name="public-client-and-confidential-client-applications"></a>Klient publiczny i poufne aplikacje klienckie
Biblioteka Microsoft Authentication Library (MSAL) definiuje dwa typy klientów: klientów publicznych i poufnych klientów. Dwa typy klientów są rozróżniane przez ich możliwość bezpiecznego uwierzytelniania z serwerem autoryzacji i zachowania poufności poświadczeń klienta. W przeciwieństwie do biblioteki Azure AD Authentication Library (ADAL) jest używany *kontekst uwierzytelniania* (który jest połączeniem z usługą Azure AD).

- **Poufne aplikacje klienckie** to aplikacje działające na serwerach (aplikacje sieci Web, aplikacje interfejsu Web API, a nawet aplikacje usługi/demon). Są one uznawane za trudne do uzyskania dostępu i z tego powodu mogą utrzymać klucz tajny aplikacji. Poufne klienci mogą przechowywać wpisy tajne czasu konfiguracji. Każde wystąpienie klienta ma odrębną konfigurację (w tym identyfikator klienta i klucz tajny klienta). Te wartości są trudne dla użytkowników końcowych do wyodrębnienia. Aplikacja sieci Web to najbardziej typowy klient poufny. Identyfikator klienta jest udostępniany za pośrednictwem przeglądarki sieci Web, ale wpis tajny jest przesyłany tylko w kanale zaplecza i nigdy nie jest bezpośrednio narażony.

    Poufne aplikacje klienckie: <BR>
    ![](media/msal-client-applications/web-app.png) ![ ](media/msal-client-applications/web-api.png) ![ Demon/usługa internetowego interfejsu API aplikacji sieci Web](media/msal-client-applications/daemon-service.png)

- **Publiczne aplikacje klienckie** to aplikacje działające na urządzeniach lub komputerach stacjonarnych lub w przeglądarce sieci Web. Nie są one zaufane, aby bezpiecznie przechowywać wpisy tajne aplikacji, więc uzyskują dostęp tylko do interfejsów API sieci Web w imieniu użytkownika. (Obsługują tylko publiczne przepływy klientów). Klienci publiczni nie mogą przechowywać wpisów tajnych czasu konfiguracji, więc nie mają kluczy tajnych klienta.

    Publiczne aplikacje klienckie: <BR>
    ![](media/msal-client-applications/desktop-app.png) ![ ](media/msal-client-applications/browserless-app.png) Aplikacja mobilna interfejsu API bezprzeglądarka aplikacji klasycznych ![](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> W MSAL.js nie ma rozdzielania publicznych i poufnych aplikacji klienckich.  MSAL.js reprezentuje aplikacje klienckie jako aplikacje oparte na agencie użytkownika, klienci publiczni, w których kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka sieci Web. Ci klienci nie przechowują wpisów tajnych, ponieważ kontekst przeglądarki jest otwarty.

## <a name="comparing-the-client-types"></a>Porównywanie typów klienta
Poniżej przedstawiono niektóre podobieństwa i różnice między klientem publicznym i poufnymi aplikacjami klienckimi:

- Oba rodzaje aplikacji utrzymują pamięć podręczną tokenów użytkownika i mogą uzyskać token dyskretnie (gdy token znajduje się już w pamięci podręcznej tokenów). Poufne aplikacje klienckie mają również pamięć podręczną tokenów aplikacji dla tokenów dla samej aplikacji.
- Oba typy aplikacji zarządzają kontami użytkowników i mogą uzyskać konto z pamięci podręcznej tokenów użytkowników, uzyskać konto z jego identyfikatora lub usunąć konto.
- Publiczne aplikacje klienckie mają cztery metody uzyskiwania tokenu (cztery przepływy uwierzytelniania). Poufne aplikacje klienckie mają trzy metody uzyskiwania tokenu (i jeden ze sposobów obliczania adresu URL dostawcy tożsamości, który autoryzują punkt końcowy). Aby uzyskać więcej informacji, zobacz [Uzyskiwanie tokenów](msal-acquire-cache-tokens.md).

Jeśli użyto biblioteki ADAL, możesz zauważyć, że w przeciwieństwie do kontekstu uwierzytelniania biblioteki ADAL w MSAL identyfikator klienta (nazywany również *identyfikatorem aplikacji* lub *identyfikatorem* aplikacji) jest przekazywany raz podczas konstruowania aplikacji. Nie trzeba go ponownie przekazywać, gdy aplikacja uzyska token. Dotyczy to zarówno publicznej, jak i poufnej aplikacji klienckiej. W przypadku konstruktorów poufnych aplikacji klienckich są również przesyłane poświadczenia klienta: klucz tajny, który udostępnia dostawcy tożsamości.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:
- [Opcje konfiguracji aplikacji klienckiej](msal-client-application-configuration.md)
- [Tworzenie wystąpienia aplikacji klienckich za pomocą MSAL.NET](msal-net-initializing-client-applications.md)
- [Tworzenie wystąpienia aplikacji klienckich przy użyciu MSAL.js](msal-js-initializing-client-applications.md)
