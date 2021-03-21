---
title: Widoczność i kontrola aplikacji za pomocą Microsoft Cloud App Security
description: Dowiedz się, jak identyfikować poziomy ryzyka aplikacji, zatrzymywać naruszenia i wycieki w czasie rzeczywistym oraz korzystać z łączników aplikacji, aby korzystać z interfejsów API dostawcy w celu zapewnienia widoczności i zarządzania.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 682549b5b99169060437c8c91d465121002edc6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259608"
---
# <a name="cloud-app-visibility-and-control"></a>Aplikacja w chmurze — widoczność i kontrola

Aby w pełni korzystać z aplikacji i usług w chmurze, zespół IT musi znaleźć odpowiednie saldo dostępu pomocniczego przy zachowaniu kontroli ochrony danych krytycznych. Microsoft Cloud App Security zapewnia zaawansowaną widoczność, kontrolę nad przejazdami danych oraz zaawansowaną analizę, aby identyfikować i zwalczać zagrożenia cybernetycznymi w ramach wszystkich usług w chmurze firmy Microsoft i innych firm.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Odnajdywanie niezatwierdzonych zasobów IT w sieci i zarządzanie nimi

Gdy Administratorzy IT są proszeni o liczbę aplikacji w chmurze, których używają pracownicy, średnio 30 lub 40, w rzeczywistości średnia jest ponad 1 000 oddzielnymi aplikacjami używanymi przez pracowników w organizacji. W tle pomocne są informacje o tym, które aplikacje są używane i jakie są poziom ryzyka. 80 procent pracowników korzysta z niezaakceptowanych oficjalnie aplikacji, które nie zostały zrecenzowane i mogą nie być zgodne z zasadami zabezpieczeń i zgodności. Ze względu na to, że pracownicy mogą uzyskiwać dostęp do Twoich zasobów i aplikacji spoza sieci firmowej, nie wystarczy już mieć zasad i zasad zapory.

Użyj funkcji odnajdywania aplikacji Microsoft Cloud (funkcja Azure Active Directory — wersja Premium P1), aby sprawdzić, które aplikacje są używane, poznać ryzyko tych aplikacji, skonfigurować zasady w celu identyfikowania nowych ryzykownych aplikacji oraz odrzucania zaakceptowania ich w sposób natywny przy użyciu urządzenia serwera proxy lub zapory.

- Odnajdywanie i identyfikowanie niezatwierdzonych zasobów IT
- Ocena i analiza
- Zarządzanie aplikacjami
- Zaawansowane raporty wykrywania IT w tle
- Kontrola zaakceptowanych aplikacji
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Odkryj i Zarządzaj w tle w sieci ](/cloud-app-security/tutorial-shadow-it)
- [Aplikacje odnalezione za pomocą Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Widoczność i kontrola sesji użytkownika 

W dzisiejszym miejscu pracy często nie wystarczy wiedzieć, co się dzieje w środowisku chmury. Chcesz zrezygnować z naruszeń i wycieków w czasie rzeczywistym, zanim pracownicy zacelowo lub przypadkowo umieściją dane i Twoją organizację. Razem z usługą Azure Active Directory (Azure AD) Microsoft Cloud App Security udostępniają te funkcje w całościowym i zintegrowanym środowisku z Kontrola dostępu warunkowego aplikacji. 

Kontrola sesji używa architektury zwrotnego serwera proxy i jest unikatowo zintegrowana z dostępem warunkowym usługi Azure AD. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych warunków. Warunki definiują użytkowników lub grupę użytkowników oraz informacje o tym, co (w których aplikacjach w chmurze) oraz o tym, gdzie (które lokalizacje i sieci) są stosowane zasady dostępu warunkowego. Po ustaleniu warunków można kierować użytkowników do Cloud App Security, gdzie można chronić dane w czasie rzeczywistym.  

Za pomocą tego formantu można:  
- Sterowanie pobraniami plików
- Monitoruj scenariusze B2B  
- Kontrolowanie dostępu do plików  
- Ochrona dokumentów przy pobieraniu  
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Zaawansowana widoczność i kontrolki aplikacji 

Łączniki aplikacji korzystają z interfejsów API dostawców aplikacji w celu zapewnienia lepszej widoczności i kontroli przez Microsoft Cloud App Security za pośrednictwem aplikacji, z którymi nawiązywane jest połączenie. Cloud App Security korzysta z interfejsów API dostarczonych przez dostawcę chmury. Każda usługa ma własne ograniczenia dotyczące struktury i interfejsu API, takie jak ograniczanie przepustowości, limity interfejsu API, dynamiczne przesunięcia czasowe okien interfejsu API i inne. Zespół produktu Cloud App Security pracował z tymi usługami w celu zoptymalizowania użycia interfejsów API i zapewnienia najlepszej wydajności. Biorąc pod uwagę różne ograniczenia usług nakładających się na interfejsy API, aparaty Cloud App Security używają ich maksymalnej dozwolonej pojemności. Niektóre operacje, takie jak skanowanie wszystkich plików w dzierżawie, wymagają wielu wywołań interfejsu API, dzięki czemu są rozłożone w dłuższym okresie. Niektóre zasady mogą działać przez kilka godzin lub dni. 
 
### <a name="learn-more"></a>Dowiedz się więcej  

- [Łączenie aplikacji w Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Następne kroki

- [Odkryj i Zarządzaj w tle w sieci ](/cloud-app-security/tutorial-shadow-it)
- [Aplikacje odnalezione za pomocą Cloud App Security ](/cloud-app-security/discovered-apps)
- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Łączenie aplikacji w Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)