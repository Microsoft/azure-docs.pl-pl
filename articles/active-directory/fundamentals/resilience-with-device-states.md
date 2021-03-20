---
title: Odporność kompilacji przy użyciu Stanów urządzeń w Azure Active Directory
description: Przewodnik dla architektów i administratorów IT do tworzenia odporności przy użyciu Stanów urządzeń
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724614"
---
# <a name="build-resilience-with-device-states"></a>Tworzenie odporności przy użyciu Stanów urządzeń

Po włączeniu [Stanów urządzeń](../devices/overview.md) w usłudze Azure AD Administratorzy mogą tworzyć [zasady dostępu warunkowego](../conditional-access/overview.md) , które kontrolują dostęp do aplikacji w oparciu o stan urządzenia. Dodanie korzyści płynących z używania urządzeń polega na tym, że spełniają one wymagania dotyczące silnych uwierzytelnień w celu uzyskania dostępu do zasobów, co zmniejsza dodatkowe żądania uwierzytelniania MFA i zwiększa odporność. 

Poniższy wykres przepływu przedstawia różne sposoby dołączania urządzeń do usługi Azure AD, które umożliwiają korzystanie z Stanów urządzeń. Możesz użyć więcej niż jednego w organizacji.

![Wykres przepływu do wybierania Stanów urządzeń](./media/resilience-with-device-states/admin-resilience-devices.png)

W przypadku korzystania z [Stanów urządzeń](../devices/overview.md)użytkownicy będą w większości przypadków korzystać z logowania jednokrotnego do zasobów za pomocą [podstawowego tokenu odświeżania](../devices/concept-primary-refresh-token.md) (PRT). PRT zawiera oświadczenia dotyczące użytkownika i urządzenia, a także może służyć do uzyskiwania tokenów uwierzytelniania w celu uzyskania dostępu do aplikacji z urządzenia. PRT jest ważny przez 14 dni i ciągle jest odnawiany, o ile użytkownik aktywnie korzysta z urządzenia, zapewniając użytkownikom dostęp do odpornego środowiska. PRT może również uzyskać wiele rozwiązań usługi uwierzytelniania wieloskładnikowego. Aby uzyskać więcej informacji, zobacz [kiedy PRT Pobiera żądania MFA](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Jak pomagają Stany urządzeń?

Gdy PRT jest używany do żądania dostępu do aplikacji, jej urządzenie, sesja i oświadczenia MFA są zaufane przez usługę Azure AD. Gdy administratorzy tworzą zasady, które wymagają kontroli opartej na urządzeniu lub kontroli uwierzytelniania wieloskładnikowego, wymagania dotyczące zasad mogą być spełnione przez stan urządzenia bez próby uwierzytelniania wieloskładnikowego. Użytkownicy nie będą widzieć dodatkowych wyświetleń uwierzytelniania wieloskładnikowego na tym samym urządzeniu. Powoduje to zwiększenie odporności na zakłócenia usługi Azure MFA lub jej zależności, takich jak dostawcy usług telekomunikacyjnych.

## <a name="how-do-i-implement-device-states"></a>Jak mogę zaimplementować Stany urządzeń?

* Włącz [dołączoną hybrydową usługę Azure AD](../devices/hybrid-azuread-join-plan.md) i [Dołącz do usługi Azure AD](../devices/azureadjoin-plan.md) dla firmowych urządzeń z systemem Windows i Wymagaj, jeśli jest to możliwe. Jeśli nie jest to możliwe, wymagaj ich rejestracji.

  Jeśli w organizacji znajdują się starsze wersje systemu Windows, Uaktualnij te urządzenia do korzystania z systemu Windows 10.

* Normalizuj dostęp do przeglądarki [Microsoft Edge](/deployedge/microsoft-edge-security-identity) lub Google Chrome z [obsługiwanymi](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [rozszerzeniami](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) , które umożliwiają bezproblemowe logowanie jednokrotne do aplikacji sieci Web przy użyciu PRT.

* W przypadku urządzeń z systemami iOS i Android przeznaczonymi do użytku osobistego lub firmowego Wdróż [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md). Oprócz usługi uwierzytelniania wieloskładnikowego i logowania bez hasła w programie aplikacja Microsoft Authenticator umożliwia logowanie jednokrotne w aplikacji natywnej za pośrednictwem uwierzytelniania obsługiwanego przez [brokera](../develop/msal-android-single-sign-on.md) i mniejszą liczbę wierszy uwierzytelniania dla użytkowników końcowych.

* W przypadku osobistych lub firmowych urządzeń z systemem iOS i Android za pomocą [zarządzania aplikacjami mobilnymi](/mem/intune/apps/app-management) można bezpiecznie uzyskać dostęp do zasobów firmy przy mniejszej liczbie żądań uwierzytelniania. 

* [Użyj wtyczki Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)](../develop/apple-sso-plugin.md). Spowoduje to zarejestrowanie urządzenia i zapewnia Logowanie jednokrotne w przeglądarce i natywne aplikacje usługi Azure AD. 

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)


Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)