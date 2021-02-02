---
title: Nieoczekiwany monit o zgodę podczas logowania się do aplikacji | Microsoft Docs
description: Jak rozwiązywać problemy, gdy użytkownik widzi monit o zgodę dla aplikacji zintegrowanej z usługą Azure AD, której nie oczekiwano
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6253458bb31388e5f7e6f84b7720cce0dc22886c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259964"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Nieoczekiwany monit o zgodę podczas logowania się do aplikacji

Wiele aplikacji, które integrują się z Azure Active Directory wymaga uprawnień do różnych zasobów do uruchomienia. Gdy te zasoby są również zintegrowane z Azure Active Directory, wymagane są uprawnienia dostępu do nich w ramach platformy zgody na korzystanie z usługi Azure AD. 

Powoduje to wyświetlenie monitu o zgodę przy pierwszym użyciu aplikacji, co jest często jednorazową operacją. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenariusze, w których użytkownicy widzą wyświetlane informacje o zgodzie

Dodatkowe monity można oczekiwać w różnych scenariuszach:

* Zestaw uprawnień wymaganych przez aplikację został zmieniony.

* Użytkownik, który pierwotnie wyraził zgodę na korzystanie z aplikacji, nie jest administratorem, a teraz inny użytkownik (niebędący administratorem) korzysta z aplikacji po raz pierwszy.

* Użytkownik, który pierwotnie wyraził zgodę na dostęp do aplikacji, był administratorem, ale nie zaakceptował w imieniu całej organizacji.

* Aplikacja korzysta z funkcji [przyrostowej i dynamicznej,](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) aby zażądać dodatkowych uprawnień po wstępnym udzieleniu zgody. Jest to często używane, gdy opcjonalne funkcje aplikacji wymagają uprawnień poza tymi, które są wymagane do obsługi funkcji bazowych.

* Zgoda została odwołana po wstępnym przypisaniu.

* Deweloper skonfigurował aplikację tak, aby wymagała monitu o wyrażenie zgody przy każdym użyciu (Uwaga: nie jest to najlepsze rozwiązanie).

## <a name="next-steps"></a>Następne kroki

-   [Aplikacje, uprawnienia i zgoda w Azure Active Directory (punkt końcowy v 1.0)](../develop/quickstart-register-app.md)

-   [Zakresy, uprawnienia i zgody w Azure Active Directory (punkt końcowy v 2.0)](../develop/v2-permissions-and-consent.md)