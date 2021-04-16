---
title: 'Zarządzanie aplikacją: Najlepsze rozwiązania i zalecenia dotyczące | Microsoft Docs'
description: Poznaj najlepsze rozwiązania i zalecenia dotyczące zarządzania aplikacjami w Azure Active Directory. Dowiedz się więcej na temat korzystania z automatycznego aprowizowania i publikowania aplikacji lokalnych za pomocą serwer proxy aplikacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9b7f312781fd4f14c5e403ad72e5978f4d01487
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379329"
---
# <a name="application-management-best-practices"></a>Najlepsze rozwiązania dotyczące zarządzania aplikacją

Ten artykuł zawiera zalecenia i najlepsze rozwiązania dotyczące zarządzania aplikacjami w usłudze Azure Active Directory (Azure AD), używania automatycznej aprowności i publikowania aplikacji lokalnych za pomocą serwer proxy aplikacji.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Zalecenia dotyczące aplikacji w chmurze i logowania pojedynczego
| Zalecenie | Komentarze |
| --- | --- |
| Sprawdź galerię aplikacji usługi Azure AD dla aplikacji  | Usługa Azure AD ma galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone przy użyciu logowania jednokrotnego (SSO) dla przedsiębiorstw. Aby uzyskać wskazówki dotyczące konfiguracji specyficzne dla aplikacji, zobacz [Samouczek dotyczący listy aplikacji SaaS.](../saas-apps/tutorial-list.md)  | 
| Korzystanie z federowanych logowania jednokrotnego opartego na saml  | Gdy aplikacja obsługuje tę funkcję, należy używać federowanych, opartych na saml logowania jednokrotnego z usługą Azure AD zamiast logowania jednokrotnego opartego na hasłach i usług ADFS.  | 
| Podpisywanie certyfikatu przy użyciu sha-256  | Usługa Azure AD domyślnie używa algorytmu SHA-256 do podpisywania odpowiedzi SAML. Użyj sha-256, chyba że aplikacja wymaga sha-1 (zobacz [opcje](certificate-signing-options.md) podpisywania certyfikatu i problem z [logowaniem aplikacji).](application-sign-in-problem-application-error.md)  | 
| Wymagaj przypisania użytkownika  | Domyślnie użytkownicy mogą uzyskać dostęp do aplikacji przedsiębiorstwa bez przypisywania do nich. Jeśli jednak aplikacja uwidacznia role lub jeśli chcesz, aby aplikacja pojawiała się na ekranie Moje aplikacje, wymaga przypisania użytkownika.  | 
| Wdrażanie Moje aplikacje dla użytkowników | [Moje aplikacje](end-user-experiences.md) jest portalem internetowym, który zapewnia użytkownikom pojedynczy punkt wejścia dla przypisanych im aplikacji `https://myapps.microsoft.com` opartych na chmurze. W związku z tym, że dodawane są dodatkowe funkcje, takie jak zarządzanie grupą i samoobsługowe resetowanie haseł, użytkownicy mogą je znaleźć w Moje aplikacje. Zobacz [Planowanie Moje aplikacje wdrożenia.](my-apps-deployment-plan.md)
| Używanie przypisania grupy  | Jeśli ta subskrypcja jest uwzględniona, przypisz grupy do aplikacji, aby można było delegować bieżące zarządzanie dostępem do właściciela grupy.  | 
| Ustanawianie procesu zarządzania certyfikatami | Maksymalny okres istnienia certyfikatu podpisywania wynosi trzy lata. Aby zapobiec lub zminimalizować przestoje z powodu wygaśnięcia certyfikatu, należy użyć ról i list dystrybucyjnych poczty e-mail, aby upewnić się, że powiadomienia o zmianach dotyczące certyfikatów są ściśle monitorowane. |

## <a name="provisioning-recommendations"></a>Zalecenia dotyczące aprowizowania
| Zalecenie | Komentarze |
| --- | --- |
| Konfigurowanie aprowizowania za pomocą aplikacji w chmurze przy użyciu samouczków | Zapoznaj się [z listą samouczków aplikacji SaaS,](../saas-apps/tutorial-list.md) aby uzyskać szczegółowe wskazówki dotyczące konfigurowania aprownictwa dla aplikacji z galerii, którą chcesz dodać. |
| Monitorowanie stanu za pomocą dzienników aprowizowania (wersja zapoznawcza) | Dzienniki [aprowizowania](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) podają szczegółowe informacje o wszystkich akcjach wykonywanych przez usługę aprowizowania, w tym o stanie poszczególnych użytkowników. |
| Przypisywanie grupy dystrybucyjnej do wiadomości e-mail z powiadomieniem o aprowizowania | Aby zwiększyć widoczność alertów krytycznych wysyłanych przez usługę aprowizowania, przypisz grupę dystrybucyjną do ustawienia Wiadomości e-mail z powiadomieniami. |


## <a name="application-proxy-recommendations"></a>serwer proxy aplikacji zalecenia
| Zalecenie | Komentarze |
| --- | --- |
| Używanie serwer proxy aplikacji dostępu zdalnego do zasobów wewnętrznych | serwer proxy aplikacji jest zalecane w przypadku dawania użytkownikom zdalnym dostępu do zasobów wewnętrznych, zastępując potrzebę stosowania sieci VPN lub zwrotnego serwera proxy. Nie jest ona przeznaczona do uzyskiwania dostępu do zasobów z sieci firmowej, ponieważ może to dodać opóźnienie.
| Korzystanie z domen niestandardowych | Skonfiguruj domeny niestandardowe dla aplikacji [](application-proxy-configure-custom-domain.md)(zobacz Konfigurowanie domen niestandardowych), aby adresy URL użytkowników i aplikacji działały wewnątrz sieci lub poza siecią. Będzie można również kontrolować znakowanie i dostosowywać adresy URL.  W przypadku korzystania z niestandardowych nazw domen należy zaplanować uzyskanie certyfikatu publicznego od zaufanego urzędu certyfikacji firmy Microsoft. Usługa Azure serwer proxy aplikacji obsługuje certyfikaty standardowe,[(z symbolami](application-proxy-wildcard.md)wieloznaczny) lub oparte na sieci SAN. (Zobacz [serwer proxy aplikacji planowania).](application-proxy-deployment-plan.md) |
| Synchronizowanie użytkowników przed wdrożeniem serwer proxy aplikacji | Przed wdrożeniem serwera proxy aplikacji zsynchronizuj tożsamości użytkowników z katalogu lokalnego lub utwórz je bezpośrednio w usłudze Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnianie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji serwera proxy aplikacji. Udostępnia również informacje o identyfikatorze użytkownika niezbędne do wykonania logowania jednokrotnego. (Zobacz [serwer proxy aplikacji planowania).](application-proxy-deployment-plan.md) |
| Postępuj zgodnie z naszymi wskazówkami dotyczącymi wysokiej dostępności i równoważenia obciążenia | Aby dowiedzieć się, jak przepływa ruch między użytkownikami, łącznikami usługi serwer proxy aplikacji i serwerami aplikacji na zadomowić się, oraz uzyskać porady dotyczące optymalizacji wydajności i równoważenia obciążenia, zobacz Wysoka dostępność i równoważenie obciążenia łączników i aplikacji usługi [serwer proxy aplikacji.](application-proxy-high-availability-load-balancing.md) |
| Używanie wielu łączników | Użyj co najmniej dwóch łączników serwer proxy aplikacji, aby zwiększyć odporność, dostępność i skalę (zobacz [serwer proxy aplikacji łączników).](application-proxy-connectors.md) Utwórz grupy łączników i upewnij się, że każda grupa łączników ma co najmniej dwa łączniki (trzy łączniki są optymalne). |
| Zlokalizuj serwery łącznika w pobliżu serwerów aplikacji i upewnij się, że są one w tej samej domenie | Aby zoptymalizować wydajność, fizycznie zlokalizuj serwer łącznika w pobliżu serwerów aplikacji (zobacz [Zagadnienia dotyczące topologii sieci).](application-proxy-network-topology.md) Ponadto serwer łącznika i serwery aplikacji internetowych powinny należeć do tej samej domeny usługi Active Directory lub powinny obejmować domeny ufające. Ta konfiguracja jest wymagana w przypadku logowania jednokrotnego Integrated Windows Authentication (IWA) i ograniczonego delegowania protokołu Kerberos (KCD). Jeśli serwery znajdują się w różnych domenach, należy użyć delegowania opartego na zasobach na potrzeby logowania jednokrotnego (zobacz [KCD for single sign-on with serwer proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Włączanie automatycznych aktualizacji łączników | Włącz automatyczne aktualizacje łączników, aby uzyskać najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednią obsługę najnowszej wersji łącznika i jednej wersji wcześniej. (Zobacz [serwer proxy aplikacji historii wersji).](application-proxy-release-version-history.md) |
| Pomijanie lokalnego serwera proxy | Aby ułatwić konserwację, skonfiguruj łącznik w celu obejścia lokalnego serwera proxy, aby łączył się on bezpośrednio z usługami platformy Azure. (Zobacz [serwer proxy aplikacji łączników i serwerów proxy).](application-proxy-configure-connectors-with-proxy-servers.md) |
| Korzystanie z usługi Azure AD serwer proxy aplikacji za pośrednictwem sieci Web serwer proxy aplikacji | Użyj usługi Azure AD serwer proxy aplikacji w przypadku większości scenariuszy lokalnych. Usługa Web serwer proxy aplikacji jest preferowana tylko w scenariuszach, które wymagają serwera proxy dla usługi AD FS i w których nie można używać domen niestandardowych w Azure Active Directory. (Zobacz [serwer proxy aplikacji migracji).](application-proxy-migration.md) |