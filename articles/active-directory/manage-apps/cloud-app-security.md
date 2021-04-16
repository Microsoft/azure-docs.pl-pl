---
title: Widoczność i kontrola aplikacji za pomocą Microsoft Cloud App Security
description: Dowiedz się, jak identyfikować poziomy ryzyka aplikacji, zatrzymywać naruszenia i przecieki w czasie rzeczywistym oraz korzystać z łączników aplikacji, aby korzystać z interfejsów API dostawcy na użytek widoczności i ładu.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377840"
---
# <a name="cloud-app-visibility-and-control"></a>Aplikacja w chmurze — widoczność i kontrola

Aby w pełni korzystać z aplikacji i usług w chmurze, zespół IT musi znaleźć odpowiednią równowagę między obsługą dostępu a zachowaniem kontroli w celu ochrony kluczowych danych. Microsoft Cloud App Security zapewnia rozbudowany wgląd, kontrolę nad podróżami danych i zaawansowaną analizę w celu identyfikowania i śledzenia zagrożeń cybernetycznych we wszystkich usługach w chmurze firmy Microsoft i innych firm.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Odnajdywanie niezatwierdzonych zasobów IT w sieci i zarządzanie nimi

Gdy administratorzy IT są pytani, ile aplikacji w chmurze sądzą ich pracownicy, średnio powiedzą 30 lub 40, gdy w rzeczywistości średnia wynosi ponad 1000 oddzielnych aplikacji używanych przez pracowników w organizacji. Shadow IT pomaga wiedzieć i identyfikować, które aplikacje są używane i jaki jest poziom ryzyka. Osiemdziesiąt procent pracowników używa niezaakcentowanych aplikacji, których nikt nie przejmuje i które mogą nie być zgodne z zasadami zabezpieczeń i zgodności. A ponieważ pracownicy mogą uzyskać dostęp do zasobów i aplikacji spoza sieci firmowej, nie wystarczy już mieć reguł i zasad w zaporach.

Użyj usługi Microsoft Cloud App Discovery (funkcji Azure Active Directory — wersja Premium P1), aby dowiedzieć się, które aplikacje są używane, zbadać ryzyko związane z tymi aplikacjami, skonfigurować zasady w celu identyfikowania nowych ryzykownych aplikacji i usunąć te aplikacje, aby zablokować je natywnie przy użyciu serwera proxy lub urządzenia zapory.

- Odnajdywanie i identyfikowanie niezatwierdzonych zasobów IT
- Ocena i analiza
- Zarządzanie aplikacjami
- Zaawansowane raportowanie odnajdywania w tle it
- Kontrolowanie aplikacji, które mają być dostępne jako aplikacje
 
### <a name="learn-more"></a>Więcej tutaj

- [Odnajdywanie niesprawnych iterów w sieci i zarządzanie nimi ](/cloud-app-security/tutorial-shadow-it)
- [Odnalezione aplikacje z Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Widoczność i kontrola sesji użytkownika 

W dzisiejszym miejscu pracy często nie wystarczy wiedzieć, co dzieje się w środowisku chmury po fakcie. Chcesz zapobiec naruszeniom i wyciekom w czasie rzeczywistym, zanim pracownicy celowo lub przypadkowo nałożyli na nie ryzyko danych i organizacji. Wraz z Azure Active Directory (Azure AD) Microsoft Cloud App Security te możliwości w całościowym i zintegrowanym doświadczeniu z Kontrola dostępu warunkowego aplikacji. 

Kontrola sesji używa architektury zwrotnego serwera proxy i jest unikatowo zintegrowana z dostępem warunkowym usługi Azure AD. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu do aplikacji organizacji na podstawie określonych warunków. Warunki określają, kto (użytkownik lub grupa użytkowników) i co (które aplikacje w chmurze) oraz gdzie (do których lokalizacji i sieci) są stosowane zasady dostępu warunkowego. Po ujednaniu warunków można przekierowyć użytkowników do Cloud App Security gdzie można chronić dane w czasie rzeczywistym.  

Za pomocą tej kontrolki można:  
- Sterowanie pobieraniem plików
- Monitorowanie scenariuszy B2B  
- Kontrola dostępu do plików  
- Ochrona dokumentów podczas pobierania  
 
### <a name="learn-more"></a>Więcej tutaj

- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Zaawansowana widoczność i kontrolki aplikacji 

Łączniki aplikacji używają interfejsów API dostawców aplikacji, aby umożliwić lepszy wgląd i kontrolę Microsoft Cloud App Security nad aplikacjami, z które się łączysz. Cloud App Security korzysta z interfejsów API dostarczonych przez dostawcę usług w chmurze. Każda usługa ma własną platformę i ograniczenia interfejsu API, takie jak ograniczanie przepustowości, limity interfejsu API, dynamiczne okna interfejsu API z przesunięciem czasu i inne. Zespół Cloud App Security produktowych pracował z tymi usługami, aby zoptymalizować użycie interfejsów API i zapewnić najlepszą wydajność. Biorąc pod uwagę różne ograniczenia, które usługi nakładają na interfejsy API, aparaty Cloud App Security używają maksymalnej dozwolonej pojemności. Niektóre operacje, takie jak skanowanie wszystkich plików w dzierżawie, wymagają wielu wywołań interfejsu API, aby były rozłożone na dłuższy czas. Niektóre zasady będą działać przez kilka godzin lub dni. 
 
### <a name="learn-more"></a>Więcej tutaj  

- [Łączenie aplikacji w Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Następne kroki

- [Odnajdywanie niesprawnych it w sieci i zarządzanie nimi ](/cloud-app-security/tutorial-shadow-it)
- [Odnalezione aplikacje z Cloud App Security ](/cloud-app-security/discovered-apps)
- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Łączenie aplikacji w Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)