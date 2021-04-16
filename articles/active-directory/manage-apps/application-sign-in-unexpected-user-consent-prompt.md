---
title: Nieoczekiwany monit o zgodę podczas logowania do aplikacji | Microsoft Docs
description: Jak rozwiązywać problemy, gdy użytkownik zobaczy monit o wyrażenie zgody dla aplikacji zintegrowanej z usługą Azure AD, która nie była oczekiwana
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378062"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Nieoczekiwany monit o zgodę podczas logowania do aplikacji

Wiele aplikacji zintegrowanych z Azure Active Directory wymaga uprawnień do różnych zasobów w celu uruchomienia. Gdy te zasoby są również zintegrowane z Azure Active Directory, wymagane są uprawnienia dostępu do nich przy użyciu platformy wyrażania zgody usługi Azure AD. 

Powoduje to, że monit o wyrażenie zgody jest wyświetlany przy pierwszym zastosowaniu aplikacji, co często jest operacją tylko raz. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenariusze, w których użytkownicy widzą monity o wyrażenie zgody

Dodatkowe monity mogą być oczekiwane w różnych scenariuszach:

* Zestaw uprawnień wymaganych przez aplikację został zmieniony.

* Użytkownik, który pierwotnie wyraził zgodę na aplikację, nie był administratorem, a teraz inny użytkownik (inny niż administrator) używa aplikacji po raz pierwszy.

* Użytkownik, który pierwotnie wyraził zgodę na aplikację, był administratorem, ale nie wyraził zgody w imieniu całej organizacji.

* Aplikacja korzysta z [przyrostowej i dynamicznej zgody na](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) żądanie dodatkowych uprawnień po początkowym przyznaniu zgody. Jest to często używane, gdy opcjonalne funkcje dodatkowej aplikacji wymagają uprawnień wykraczających poza te, które są wymagane dla funkcjonalności linii bazowej.

* Zgoda została odwołana po początkowym przyznaniu.

* Deweloper skonfigurował aplikację tak, aby wymagała monitu o wyrażenie zgody za każdym razem, gdy jest używana (uwaga: nie jest to najlepsze rozwiązanie).

## <a name="next-steps"></a>Następne kroki

-   [Aplikacje, uprawnienia i zgody w Azure Active Directory (punkt końcowy w wersji 1.0)](../develop/quickstart-register-app.md)

-   [Zakresy, uprawnienia i zgody w Azure Active Directory (punkt końcowy w wersji 2.0)](../develop/v2-permissions-and-consent.md)
