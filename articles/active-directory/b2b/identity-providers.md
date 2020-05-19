---
title: Dostawcy tożsamości dla tożsamości zewnętrznych — Azure AD
description: Azure Active Directory współpracy B2B obsługuje uwierzytelnianie wieloskładnikowe (MFA) na potrzeby selektywnego dostępu do aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598150"
---
# <a name="identity-providers-for-external-identities"></a>Dostawcy tożsamości dla tożsamości zewnętrznych

*Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. W przypadku udostępniania aplikacji i zasobów użytkownikom zewnętrznym usługa Azure AD jest domyślnym dostawcą tożsamości na potrzeby udostępniania. Oznacza to, że w przypadku zapraszania użytkowników zewnętrznych, którzy już mają usługę Azure AD lub konto Microsoft, mogą oni automatycznie logować się bez dalszej konfiguracji.

Można jednak umożliwić użytkownikom logowanie się przy użyciu różnych dostawców tożsamości. Można na przykład skonfigurować Federacji z dostawcami tożsamości społecznościowych, które są obsługiwane przez usługę Azure AD, w tym Google i Facebook. Można także sfederować z dowolnym zewnętrznym dostawcą tożsamości, który obsługuje protokoły SAML lub WS-karmione. Za pomocą Federacji zewnętrznego dostawcy tożsamości można zaoferować użytkownikom zewnętrznym możliwość zalogowania się do aplikacji przy użyciu istniejących kont społecznościowych lub firmowych.

## <a name="how-it-works"></a>Jak to działa

Tożsamości zewnętrzne usługi Azure AD są wstępnie skonfigurowane dla Federacji z usługami Google i Facebook. Aby skonfigurować tych dostawców tożsamości w dzierżawie usługi Azure AD, należy utworzyć aplikację dla każdego dostawcy tożsamości i skonfigurować poświadczenia. Uzyskasz identyfikator klienta lub aplikacji oraz klucz tajny klienta lub aplikacji, który można następnie dodać do dzierżawy usługi Azure AD.

Po dodaniu dostawcy tożsamości do dzierżawy usługi Azure AD:

- W przypadku zapraszania użytkownika zewnętrznego do aplikacji lub zasobów w organizacji użytkownik zewnętrzny może zalogować się przy użyciu własnego konta z tym dostawcą tożsamości.
- Po włączeniu samoobsługowego [rejestrowania się](self-service-sign-up-overview.md) w aplikacjach użytkownicy zewnętrzni mogą zarejestrować się w aplikacjach przy użyciu własnych kont z dodanymi dostawcami tożsamości. 

Podczas realizowania zaproszenia lub rejestracji w aplikacji użytkownik zewnętrzny ma możliwość logowania się i uwierzytelniania za pomocą dostawcy tożsamości społecznościowej:

![Zrzut ekranu przedstawiający ekran logowania z opcjami Google i Facebook](media/identity-providers/sign-in-with-social-identity.png)

Aby zapewnić optymalne środowisko logowania, sfederować się z dostawcami tożsamości wszędzie tam, gdzie to możliwe, aby zaproszony Goście mogli bezproblemowo korzystać z aplikacji.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać dostawców tożsamości do logowania się do aplikacji, zapoznaj się z następującymi artykułami:

- [Dodaj firmę Google](google-federation.md) do listy dostawców tożsamości społecznościowych
- [Dodawanie usługi Facebook](facebook-federation.md) do listy dostawców tożsamości społecznościowych
- [Skonfiguruj bezpośrednią Federacji](direct-federation.md) z każdą organizacją, której dostawca tożsamości obsługuje protokół SAML 2,0 lub WS-Direction. Należy zauważyć, że Federacja bezpośrednia nie jest opcją dla przepływów użytkownika samoobsługowego rejestrowania.
