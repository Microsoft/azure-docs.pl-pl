---
title: 'Zarządzanie aplikacjami: najlepsze rozwiązania i zalecenia | Microsoft Docs'
description: Poznaj najlepsze rozwiązania i zalecenia dotyczące zarządzania aplikacjami w Azure Active Directory. Dowiedz się więcej o używaniu automatycznej aprowizacji i publikowania aplikacji lokalnych przy użyciu serwera proxy aplikacji.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5aaf4bbff4c093872228d647d3850667fb9777
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656411"
---
# <a name="application-management-best-practices"></a>Najlepsze rozwiązania dotyczące zarządzania aplikacjami

Ten artykuł zawiera zalecenia i najlepsze rozwiązania dotyczące zarządzania aplikacjami w usłudze Azure Active Directory (Azure AD) przy użyciu automatycznej aprowizacji i publikowania aplikacji lokalnych przy użyciu serwera proxy aplikacji.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Zalecenia dotyczące aplikacji w chmurze i logowania jednokrotnego
| Zalecenie | Komentarze |
| --- | --- |
| Sprawdź galerię aplikacji usługi Azure AD dla aplikacji  | Usługa Azure AD zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone przy użyciu logowania jednokrotnego w przedsiębiorstwie. Aby uzyskać wskazówki dotyczące instalacji specyficzne dla aplikacji, zobacz [listę samouczków aplikacji SaaS](../saas-apps/tutorial-list.md).  | 
| Używanie federacyjnego logowania jednokrotnego opartego na protokole SAML  | Gdy aplikacja je obsługuje, użyj federacyjnego, opartego na języku SAML logowania jednokrotnego w usłudze Azure AD zamiast logowania jednokrotnego i usług ADFS opartych na hasłach.  | 
| Użycie algorytmu SHA-256 do podpisywania certyfikatu  | Usługa Azure AD domyślnie używa algorytmu SHA-256 do podpisywania odpowiedzi SAML. Użyj algorytmu SHA-256, chyba że aplikacja wymaga algorytmu SHA-1 (zobacz [Opcje podpisywania certyfikatu](certificate-signing-options.md) i [problem z logowaniem do aplikacji](application-sign-in-problem-application-error.md)).  | 
| Wymagaj przypisania użytkownika  | Domyślnie użytkownicy mogą uzyskiwać dostęp do aplikacji firmowych bez ich przypisywania. Jeśli jednak aplikacja uwidacznia role lub jeśli chcesz, aby aplikacja była wyświetlana w aplikacjach użytkownika, wymagaj przypisania użytkownika. (Zobacz [wskazówki dla deweloperów dotyczące integrowania aplikacji](developer-guidance-for-integrating-applications.md)).  | 
| Wdróż moje aplikacje dla użytkowników | [Moje aplikacje](end-user-experiences.md) w programie `https://myapps.microsoft.com` to portal oparty na sieci Web, który udostępnia użytkownikom pojedynczy punkt wejścia dla przypisanych aplikacji opartych na chmurze. W miarę dodawania dodatkowych funkcji, takich jak zarządzanie grupami i Samoobsługowe resetowanie haseł, użytkownicy mogą je znaleźć w moich aplikacjach. Zobacz [Planowanie wdrażania aplikacji](access-panel-deployment-plan.md).
| Użyj przypisania grupy  | W przypadku uwzględnienia w subskrypcji Przypisz grupy do aplikacji, aby można było delegować bieżące zarządzanie dostępem do właściciela grupy. (Zobacz [wskazówki dla deweloperów dotyczące integrowania aplikacji](developer-guidance-for-integrating-applications.md)).   | 
| Ustanów proces zarządzania certyfikatami | Maksymalny okres istnienia certyfikatu podpisywania wynosi trzy lata. Aby zapobiec lub zminimalizować przestoje ze względu na wygaśnięcie certyfikatu, należy użyć ról i list dystrybucyjnych poczty e-mail, aby upewnić się, że powiadomienia o zmianach powiązane z certyfikatami są ściśle monitorowane. |

## <a name="provisioning-recommendations"></a>Zalecenia dotyczące aprowizacji
| Zalecenie | Komentarze |
| --- | --- |
| Korzystanie z samouczków w celu skonfigurowania aprowizacji przy użyciu aplikacji w chmurze | Zapoznaj się z [listą samouczków aplikacji SaaS](../saas-apps/tutorial-list.md) , aby uzyskać wskazówki krok po kroku dotyczące konfigurowania aprowizacji dla aplikacji galerii, którą chcesz dodać. |
| Użyj dzienników aprowizacji (wersja zapoznawcza) do monitorowania stanu | [Dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zawierają szczegółowe informacje o wszystkich akcjach wykonywanych przez usługę aprowizacji, w tym o stanie poszczególnych użytkowników. |
| Przypisywanie grupy dystrybucji do wiadomości e-mail z powiadomieniem o aprowizacji | Aby zwiększyć widoczność alertów krytycznych wysyłanych przez usługę aprowizacji, należy przypisać grupę dystrybucyjną do ustawienia powiadomienia E-mail. |


## <a name="application-proxy-recommendations"></a>Zalecenia dotyczące serwera proxy aplikacji
| Zalecenie | Komentarze |
| --- | --- |
| Używanie serwera proxy aplikacji do dostępu zdalnego do zasobów wewnętrznych | Serwer proxy aplikacji jest zalecany do zapewniania użytkownikom zdalnym dostępu do zasobów wewnętrznych, zastępując potrzebę sieci VPN lub zwrotnego serwera proxy. Nie jest przeznaczona do uzyskiwania dostępu do zasobów z sieci firmowej, ponieważ może to spowodować dodanie opóźnienia.
| Korzystanie z domen niestandardowych | Skonfiguruj niestandardowe domeny dla aplikacji (zobacz [Konfigurowanie domen niestandardowych](application-proxy-configure-custom-domain.md)), aby adresy URL użytkowników i między aplikacjami działały zarówno wewnątrz sieci, jak i poza nią. Będziesz również w stanie kontrolować markę i dostosowywać adresy URL.  W przypadku korzystania z niestandardowych nazw domen należy zaplanować uzyskanie certyfikatu publicznego z zaufanego urzędu certyfikacji niepochodzącego od firmy Microsoft. Serwer proxy aplikacji platformy Azure obsługuje standardowe, ([wieloznaczne](application-proxy-wildcard.md)) lub certyfikaty oparte na sieci SAN. (Zobacz [Planowanie serwera proxy aplikacji](application-proxy-deployment-plan.md)). |
| Synchronizuj użytkowników przed wdrożeniem serwera proxy aplikacji | Przed wdrożeniem serwera proxy aplikacji zsynchronizuj tożsamości użytkowników z katalogu lokalnego lub utwórz je bezpośrednio w usłudze Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnienie użytkowników przed udzieleniem im dostępu do opublikowanych aplikacji serwera proxy aplikacji. Zawiera również informacje o identyfikatorze użytkownika niezbędne do przeprowadzenia rejestracji jednokrotnej (SSO). (Zobacz [Planowanie serwera proxy aplikacji](application-proxy-deployment-plan.md)). |
| Postępuj zgodnie z naszymi wskazówkami dotyczącymi wysokiej dostępności i równoważenia obciążenia | Aby dowiedzieć się, jak przepływy ruchu między użytkownikami, łącznikami serwera proxy aplikacji i serwerami aplikacji zaplecza oraz uzyskać wskazówki dotyczące optymalizowania wydajności i równoważenia obciążenia, zobacz [wysoka dostępność i równoważenie obciążenia łączników i aplikacji serwera proxy aplikacji](application-proxy-high-availability-load-balancing.md). |
| Używanie wielu łączników | Użyj dwóch lub więcej łączników serwera proxy aplikacji w celu uzyskania większej odporności, dostępności i skalowania (zobacz [Łączniki serwera proxy aplikacji](application-proxy-connectors.md)). Utwórz grupy łączników i upewnij się, że każda grupa łączników ma co najmniej dwa łączniki (optymalnie są trzy łączniki). |
| Lokalizowanie serwerów łączników blisko serwerów aplikacji i upewnij się, że znajdują się one w tej samej domenie | Aby zoptymalizować wydajność, fizycznie zlokalizuj serwer łącznika blisko serwerów aplikacji (zobacz [zagadnienia dotyczące topologii sieci](application-proxy-network-topology.md)). Ponadto serwer łącznika i serwery aplikacji sieci Web powinny należeć do tej samej domeny Active Directory lub powinny obejmować relacje zaufania domen. Ta konfiguracja jest wymagana w przypadku logowania jednokrotnego przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) i ograniczonego delegowania protokołu Kerberos (KCD). Jeśli serwery znajdują się w różnych domenach, należy użyć delegowania opartego na zasobach na potrzeby logowania jednokrotnego (zobacz KCD Logowanie jednokrotne [przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Włącz autoaktualizacje dla łączników | Włącz autoaktualizacje dla łączników, aby zapoznać się z najnowszymi funkcjami i poprawkami błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji łącznika i jednej wersji. (Zobacz [historia wersji serwera proxy aplikacji](application-proxy-release-version-history.md)). |
| Pomijanie lokalnego serwera proxy | Aby ułatwić konserwację, należy skonfigurować łącznik tak, aby pomijał lokalny serwer proxy, aby łączył się bezpośrednio z usługami platformy Azure. (Zobacz [Łączniki serwera proxy aplikacji i serwery proxy](application-proxy-configure-connectors-with-proxy-servers.md)). |
| Korzystanie z usługi Azure serwer proxy aplikacji usługi Azure AD przez serwer proxy aplikacji sieci Web | Użyj usługi Azure serwer proxy aplikacji usługi Azure AD w przypadku większości scenariuszy lokalnych. Serwer proxy aplikacji sieci Web jest preferowany tylko w scenariuszach, które wymagają serwera proxy dla AD FS i nie można używać domen niestandardowych w programie Azure Active Directory. (Zobacz [Migracja serwera proxy aplikacji](application-proxy-migration.md)). |