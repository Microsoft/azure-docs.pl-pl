---
title: Tworzenie odporności w infrastrukturze IAM przy użyciu Azure Active Directory
description: Przewodnik dla architektów i administratorów IT nietworzący odporności na zakłócenia infrastruktury usługi IAM.
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
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919894"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Tworzenie odporności w infrastrukturze zarządzania tożsamościami i dostępem

Azure Active Directory jest globalnym systemem zarządzania tożsamościami i dostępem w chmurze, który zapewnia usługi krytyczne, takie jak uwierzytelnianie i autoryzacja dla zasobów organizacji. Ten dokument zawiera wskazówki ułatwiające zrozumienie, zawieranie i łagodzenie ryzyka zakłócenia usług uwierzytelniania lub autoryzacji zasobów opartych na Azure Active Directory (Azure AD). 

Zestaw dokumentów został zaprojektowany dla

* Architektzy tożsamości

* Właściciele usługi tożsamości

* Zespoły operacji tożsamości

Zapoznaj się również z dokumentacją dla [deweloperów aplikacji](https://aka.ms/azureadresilience/developer) i [systemów Azure AD B2C](resilience-b2c.md).

## <a name="what-is-resilience"></a>Co to jest odporność?

W kontekście infrastruktury tożsamości odporność jest zdolność do trwałego zakłócenia usług, takich jak uwierzytelnianie i autoryzacja, lub Niepowodzenie innych składników, z minimalnym lub brakiem wpływu na działalność, użytkowników i operacje. Wpływ zakłóceń może być poważny, a odporność wymaga sumienniego planowania.

## <a name="why-worry-about-disruption"></a>Dlaczego warto martwić się o zakłócenia?

Każde wywołanie systemu uwierzytelniania może ulec zakłóceniu, jeśli jakikolwiek składnik w łańcuchu wywołania usługi Azure AD zakończy się niepowodzeniem. Oznacza to, że jeśli jakakolwiek część infrastruktury ma problem z, można przerwać pracę, ponieważ użytkownicy nie mogą uzyskać dostępu do potrzebnych aplikacji. W związku z tym zmniejszenie liczby wywołań uwierzytelniania i liczby zależności w tych wywołaniach jest istotne dla odporności. Deweloperzy aplikacji mogą zapewnić kontrolę nad częstotliwością żądania tokenów. Możesz na przykład współpracować z deweloperami, aby upewnić się, że korzystają z tożsamości zarządzanych usługi Azure AD dla swoich aplikacji, gdy jest to możliwe. 

W systemie uwierzytelniania opartego na tokenach, takim jak Azure AD, aplikacja użytkownika (klient) musi uzyskać token zabezpieczający z systemu tożsamości, zanim będzie mógł uzyskać dostęp do aplikacji lub innego zasobu. W trakcie okresu ważności klient może wielokrotnie przedstawić ten sam token, aby uzyskać dostęp do aplikacji.

Gdy token przedstawiony dla aplikacji wygaśnie, aplikacja odrzuca token, a klient musi uzyskać nowy token z usługi Azure AD. Uzyskanie nowego tokenu może potencjalnie wymagać interakcji z użytkownikiem, na przykład z prośbami o poświadczenia. Zmniejszenie częstotliwości wywołań uwierzytelniania z tokenami o dłuższym obniżyć to ryzyko. Należy jednak zrównoważyć czas życia tokenu z ryzykiem utworzonym przez mniejszą liczbę ocen zasad. Aby uzyskać więcej informacji o zarządzaniu okresami istnienia tokenu, zapoznaj się z tym artykułem dotyczącym [optymalizacji ponownych uwierzytelnień](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

## <a name="ways-to-increase-resilience"></a>Sposoby zwiększenia odporności
Na poniższym diagramie przedstawiono sześć konkretnych sposobów zwiększania odporności. Każda metoda została szczegółowo omówiona w artykułach powiązanych w następnych krokach tego artykułu.
  
![Diagram przedstawiający omówienie odporności administratorów](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)
