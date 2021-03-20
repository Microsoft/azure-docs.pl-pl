---
title: Wymagania dotyczące tożsamości dla projektowania tożsamości hybrydowej w chmurze Azure | Microsoft Docs
description: Zidentyfikuj potrzeby biznesowe firmy, które spowodują zdefiniowanie wymagań dotyczących projektu tożsamości hybrydowej.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89660565"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Określ wymagania dotyczące tożsamości dla rozwiązania do tworzenia tożsamości hybrydowej
Pierwszym krokiem projektowania rozwiązania tożsamości hybrydowej jest określenie wymagań organizacji biznesowej, które będą korzystać z tego rozwiązania.  Tożsamość hybrydowa jest uruchamiana jako rola pomocnicza (obsługuje ona wszystkie inne rozwiązania w chmurze, zapewniając uwierzytelnianie) i jest dostępna w celu zapewnienia nowych i interesujących funkcji, które blokują nowe obciążenia dla użytkowników.  Te obciążenia lub usługi, które mają zostać przyjęte dla użytkowników, będą określać wymagania dotyczące projektu tożsamości hybrydowej.  Te usługi i obciążenia muszą korzystać z tożsamości hybrydowej zarówno lokalnie, jak i w chmurze.  

Należy zapoznać się z kluczowymi aspektami biznesowymi, aby zrozumieć, co jest wymagane teraz, i co firma planuje w przyszłości. Jeśli nie masz wglądu w długoterminową strategię projektowania tożsamości hybrydowej, prawdopodobnie Twoje rozwiązanie nie będzie skalowalne w miarę wzrostu i zmiany potrzeb firmy. Na poniższym diagramie przedstawiono przykład architektury tożsamości hybrydowej oraz obciążeń, które są odblokowywane dla użytkowników. Jest to tylko przykład wszystkich nowych możliwości, które mogą zostać odblokowane i dostarczone z pełną strategią tożsamości hybrydowej. 

Niektóre składniki stanowiące część hybrydowej ![ architektury tożsamości hybrydowej architektury tożsamości](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Określanie potrzeb firmy
Każda firma będzie miała inne wymagania, nawet jeśli te firmy są częścią tej samej branży, rzeczywiste wymagania biznesowe mogą się różnić. Nadal można korzystać z najlepszych rozwiązań z branży, ale ostatecznie są to potrzeby biznesowe firmy, które spowodują zdefiniowanie wymagań dotyczących projektu tożsamości hybrydowej. 

Upewnij się, że masz odpowiedź na następujące pytania, aby zidentyfikować potrzeby biznesowe:

* Czy Twoja firma poszukuje kosztów operacyjnych IT?
* Czy firma chce zabezpieczyć zasoby w chmurze (SaaS aplikacje, infrastruktura)?
* Czy Twoja firma chce przeprowadzić modernizację IT?
  * Czy użytkownicy będą mogli korzystać z aplikacji mobilnych i wymagających do tworzenia wyjątków w strefie DMZ, aby zezwolić na ruch różnych zasobów do różnych typów ruchu?
  * Czy Twoja firma ma starsze aplikacje, które muszą być publikowane dla tych nowoczesnych użytkowników, ale nie są łatwe do ponownego pisania?
  * Czy firma musi wykonać wszystkie te zadania i kontrolować ją w tym samym czasie?
* Czy Twoja firma chce zabezpieczyć tożsamość użytkowników i zmniejszyć ryzyko dzięki udostępnieniu nowych narzędzi, które wykorzystują wiedzę z zakresu bezpieczeństwa platformy Azure firmy Microsoft?
* Czy Twoja firma próbuje usunąć konta dreaded "External" w lokalu i przenieść je do chmury, gdzie nie jest już zagrożeniem w środowisku lokalnym?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizowanie lokalnej infrastruktury tożsamości
Teraz, gdy masz pomysł dotyczący wymagań firmy obowiązujących w firmie, musisz oszacować lokalną infrastrukturę tożsamości. Ta ocena jest ważna w przypadku definiowania wymagań technicznych związanych z integracją bieżącego rozwiązania do zarządzania tożsamościami w chmurze. Upewnij się, że odpowiadają na następujące pytania:

* Jakie rozwiązanie uwierzytelniania i autoryzacji ma być używane lokalnie przez firmę? 
* Czy w firmie są obecnie dostępne usługi synchronizacji lokalnej?
* Czy firma korzysta z dostawców tożsamości innych firm (dostawcy tożsamości)?

Należy również znać usługi w chmurze, które mogą mieć firma. Bardzo ważne jest przeprowadzenie oceny, aby zrozumieć aktualną integrację z modelami SaaS, IaaS lub PaaS w danym środowisku. Podczas tej oceny należy odpowiedzieć na następujące pytania:

* Czy Twoja firma ma integrację z dostawcą usług w chmurze?
* Jeśli tak, które usługi są używane?
* Czy ta integracja jest obecnie w środowisku produkcyjnym, czy też jest pilotażem?

> [!NOTE]
> Cloud Discovery analizuje dzienniki ruchu w odniesieniu do wykazu aplikacji w chmurze Microsoft Cloud App Security o ponad 16 000 aplikacji w chmurze, które są klasyfikowane i oceniane na podstawie ponad 70 czynników ryzyka, aby zapewnić ciągły wgląd w użycie chmury, jej cień oraz zagrozić ryzykiem w organizacji. Aby rozpocząć, zobacz [konfigurowanie Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Oceń wymagania dotyczące integracji tożsamości
Następnie należy oszacować wymagania dotyczące integracji tożsamości. Ta ocena jest ważna, aby zdefiniować wymagania techniczne dotyczące sposobu uwierzytelniania użytkowników, jak obecność organizacji będzie wyglądać w chmurze, jak organizacja zezwoli na autoryzację i jakie środowisko użytkownika będzie miało być. Upewnij się, że odpowiadają na następujące pytania:

* Czy Twoja organizacja będzie używać Federacji, uwierzytelniania standardowego czy obu tych usług?
* Czy jest wymagana Federacja?  Z powodu następujących elementów:
  * Logowanie jednokrotne oparte na protokole Kerberos
  * Firma dysponuje aplikacjami lokalnymi (wbudowaną wewnętrznie lub inną firmą), która używa protokołu SAML lub podobnych możliwości federacyjnych.
  * Uwierzytelnianie wieloskładnikowe za pośrednictwem kart inteligentnych. SecurID RSA itd.
  * Reguły dostępu klienta, które dotyczą poniższych pytań:
    1. Czy mogę zablokować dostęp zewnętrzny do Microsoft 365 na podstawie adresu IP klienta?
    2. Czy mogę zablokować dostęp zewnętrzny do Microsoft 365, z wyjątkiem programu Exchange ActiveSync?
    3. Czy mogę zablokować dostęp zewnętrzny do Microsoft 365, z wyjątkiem aplikacji opartych na przeglądarce (OWA, SPO)
    4. Czy mogę zablokować dostęp zewnętrzny do Microsoft 365 dla członków wydzielonych grup usługi AD
* Zagadnienia dotyczące zabezpieczeń/inspekcji
* Już istniejące inwestycje w uwierzytelnianie federacyjne
* Jakiej nazwy Nasza organizacja będzie używać dla naszej domeny w chmurze?
* Czy organizacja ma domenę niestandardową?
  1. Czy domena ma być publiczna i łatwa do zweryfikowania za pośrednictwem usługi DNS?
  2. Jeśli tak nie jest, czy masz domenę publiczną, której można użyć do zarejestrowania alternatywnej nazwy UPN w usłudze AD?
* Czy identyfikatory użytkowników są spójne dla reprezentacji w chmurze? 
* Czy organizacja ma aplikacje wymagające integracji z usługami w chmurze?
* Czy organizacja ma wiele domen i czy wszystkie będą używać uwierzytelniania standardowego czy federacyjnego?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Oceń aplikacje działające w środowisku
Teraz, gdy masz pomysł dotyczący infrastruktury lokalnej i chmurowej, musisz oszacować aplikacje, które działają w tych środowiskach. Ta ocena jest ważna w celu zdefiniowania wymagań technicznych w celu integracji tych aplikacji z systemem zarządzania tożsamościami w chmurze. Upewnij się, że odpowiadają na następujące pytania:

* Gdzie będą się znajdować nasze aplikacje?
* Czy użytkownicy będą uzyskiwać dostęp do aplikacji lokalnych?  W chmurze? Lub oba?
* Czy istnieją plany podjęcia istniejących obciążeń aplikacji i przenoszenia ich do chmury?
* Czy istnieją plany tworzenia nowych aplikacji, które będą znajdować się lokalnie lub w chmurze, które będą korzystały z uwierzytelniania w chmurze?

## <a name="evaluate-user-requirements"></a>Oceń wymagania użytkowników
Należy również oszacować wymagania użytkownika. Ta ocena jest ważna w celu zdefiniowania kroków, które będą potrzebne do tworzenia i wspomagania użytkowników podczas przejścia do chmury. Upewnij się, że odpowiadają na następujące pytania:

* Czy użytkownicy będą uzyskiwać dostęp do aplikacji lokalnie?
* Czy użytkownicy będą uzyskiwać dostęp do aplikacji w chmurze?
* Jak użytkownicy zazwyczaj logują się do swojego środowiska lokalnego?
* Jak użytkownicy będą logować się do chmury?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określenie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) spowoduje przejście do opcji dostępnych i informatyków/wad każdej opcji.  Po udzieleniu odpowiedzi na te pytania należy wybrać opcję, która najlepiej odpowiada potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

