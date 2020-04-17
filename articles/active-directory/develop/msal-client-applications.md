---
title: Publiczne i poufne aplikacje klienckie (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o publicznych aplikacjach klienckich i poufnych w bibliotece uwierzytelniania firmy Microsoft (MSAL).
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
ms.openlocfilehash: 9c3292a31e5f750c16933acf94509e0ad226080a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534316"
---
# <a name="public-client-and-confidential-client-applications"></a>Publiczne aplikacje klientów i klientów poufnych
Biblioteka uwierzytelniania firmy Microsoft (MSAL) definiuje dwa typy klientów: klientów publicznych i klientów poufnych. Dwa typy klientów różnią się ich zdolność do bezpiecznego uwierzytelniania z serwerem autoryzacji i zachować poufność swoich poświadczeń klienta. Z kolei biblioteka uwierzytelniania usługi Azure AD (ADAL) używa tak zwanego *kontekstu uwierzytelniania* (który jest połączeniem z usługą Azure AD).

- **Poufne aplikacje klienckie** to aplikacje uruchamiane na serwerach (aplikacje sieci Web, internetowe aplikacje interfejsu API, a nawet aplikacje usługi/demonów). Są one uważane za trudne do uzyskania dostępu i z tego powodu mogą zachować tajność aplikacji. Klienci poufni mogą przechowywać wpisy tajne w czasie konfiguracji. Każde wystąpienie klienta ma odrębną konfigurację (w tym identyfikator klienta i klucz tajny klienta). Te wartości są trudne dla użytkowników końcowych do wyodrębnienia. Aplikacja internetowa jest najczęstszym klientem poufnym. Identyfikator klienta jest narażony za pośrednictwem przeglądarki sieci web, ale klucz tajny jest przekazywany tylko w kanale wstecznym i nigdy nie jest bezpośrednio narażony.

    Poufne aplikacje klienckie: <BR>
    ![Demon/usługa](media/msal-client-applications/web-api.png) ![interfejsu API sieci Web aplikacji](media/msal-client-applications/web-app.png) ![sieci Web](media/msal-client-applications/daemon-service.png)

- **Publiczne aplikacje klienckie** to aplikacje uruchamiane na urządzeniach lub komputerach stacjonarnych lub w przeglądarce sieci Web. Nie są one zaufane, aby bezpiecznie zachować wpisy tajne aplikacji, więc tylko dostęp do interfejsów API sieci web w imieniu użytkownika. (Obsługują one tylko przepływy klientów publicznych.) Klienci publiczni nie mogą przechowywać wpisów tajnych w czasie konfiguracji, więc nie mają wpisów tajnych klienta.

    Publiczne aplikacje klienckie: <BR>
    ![Aplikacja](media/msal-client-applications/desktop-app.png) ![komputerowa](media/msal-client-applications/browserless-app.png) ![Browserless API Mobile](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> W pliku MSAL.js nie ma rozdzielenia publicznych i poufnych aplikacji klienckich.  MSAL.js reprezentuje aplikacje klienckie jako aplikacje oparte na agentach użytkowników, klientów publicznych, w których kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka internetowa. Ci klienci nie przechowują wpisów tajnych, ponieważ kontekst przeglądarki jest otwarty.

## <a name="comparing-the-client-types"></a>Porównywanie typów klientów
Oto kilka podobieństw i różnic między klientami publicznymi a poufnymi aplikacjami klienckimi:

- Oba rodzaje aplikacji utrzymują pamięć podręczną tokenu użytkownika i mogą uzyskać token w trybie dyskretnym (gdy token jest już w pamięci podręcznej tokenu). Poufne aplikacje klienckie mają również pamięć podręczną tokenów tokenów aplikacji, które są dla samej aplikacji.
- Oba typy aplikacji zarządzają kontami użytkowników i mogą uzyskać konto z pamięci podręcznej tokenu użytkownika, uzyskać konto z jego identyfikatora lub usunąć konto.
- Publiczne aplikacje klienckie mają cztery sposoby uzyskania tokenu (cztery przepływy uwierzytelniania). Poufne aplikacje klienckie mają trzy sposoby uzyskania tokenu (i jeden sposób obliczania adresu URL dostawcy tożsamości autoryzować punkt końcowy). Aby uzyskać więcej informacji, zobacz [Pobieranie tokenów](msal-acquire-cache-tokens.md).

Jeśli używasz usługi ADAL, można zauważyć, że w przeciwieństwie do kontekstu uwierzytelniania ADAL, w msal identyfikator klienta (nazywany również *identyfikatorem aplikacji* lub *identyfikatorem aplikacji)* jest przekazywany raz przy budowie aplikacji. Nie musi być przekazywane ponownie, gdy aplikacja uzyskuje token. Dotyczy to zarówno publicznych, jak i poufnych aplikacji klienckich. Konstruktory poufnych aplikacji klienckich są również przekazywane poświadczenia klienta: klucz tajny, które udostępniają dostawcy tożsamości.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:
- [Opcje konfiguracji aplikacji klienckiej](msal-client-application-configuration.md)
- [Tworzenie wystąpienia aplikacji klienckich przy użyciu MSAL.NET](msal-net-initializing-client-applications.md)
- [Tworzenie wystąpienia aplikacji klienckich przy użyciu pliku MSAL.js](msal-js-initializing-client-applications.md)
