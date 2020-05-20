---
title: Integruj swoją ofertę komercyjnej witryny Microsoft Marketplace z Azure Active Directory
description: Użyj Azure Active Directory do uwierzytelniania Microsoft AppSource i ofert w portalu Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 17cbfe92744ad96f2b5651b7e2f47a6443337068
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658043"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Zintegruj swoją aukcję komercyjną Marketplace z usługą Azure Active Directory

 W tym artykule objaśniono wymagania dotyczące integrowania komercyjnej oferty rynkowej z usługą Azure Active Directory (Azure AD). Azure AD to usługa tożsamości w chmurze, która używa standardów branżowych, aby umożliwić uwierzytelnianie za pomocą konto Microsoft. [Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Klienci korzystający z usług Microsoft AppSource i Azure Marketplace wykorzystują środowiska w ramach produktu w celu wyszukiwania katalogów list w sklepie. Te akcje wymagają od klientów zalogowania się do produktu. Integracja z usługą Azure AD zapewnia następujące korzyści:

- Szybsza zaangażowanie i zoptymalizowane środowisko klienta
- Logowanie jednokrotne (SSO) dla milionów użytkowników w przedsiębiorstwach
- Spójne środowisko logowania między aplikacjami opublikowanymi przez różnych partnerów
- Skalowalne, międzyplatformowe uwierzytelnianie dla aplikacji mobilnych i w chmurze

## <a name="offers-that-require-azure-ad"></a>Oferty wymagające usługi Azure AD

Różne [Opcje i typy ofert](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) dostępne w komercyjnej witrynie Marketplace mają różne wymagania dotyczące implementacji usługi Azure AD. Szczegółowe informacje znajdują się w poniższej tabeli.

| **Typ oferty**    | **Wymagany jest logowanie jednokrotne usługi Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Skontaktuj się z nami | Wersja próbna | Wersja testowa | Transact |
| Maszyna wirtualna | Nie dotyczy | Nie | Nie | Nie |
| Aplikacje platformy Azure (szablon rozwiązania)  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Aplikacje zarządzane  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie |
| SaaS  | Nie | Yes | Tak | Tak |
| Containers  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie |
| Usługi konsultingowe  | Nie | Nie dotyczy | Nie dotyczy | Nie dotyczy |

Więcej informacji o wymaganiach technicznych SaaS można znaleźć w temacie [SaaS Applications oferują Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

- Aby uzyskać informacje na temat włączania logowania jednokrotnego przez integrację usługi Azure AD z listą, zobacz [Azure Active Directory dla deweloperów]( https://docs.microsoft.com/azure/active-directory/develop/).
- Aby uzyskać szczegółowe informacje na temat logowania jednokrotnego w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Włącz listę próbną

Zautomatyzowana konfiguracja klienta może zwiększyć prawdopodobieństwo konwersji. Gdy klient wybierze swoją listę próbną i zostanie przekierowany do środowiska w wersji próbnej, można skonfigurować klienta bezpośrednio bez konieczności wykonywania dodatkowych czynności związanych z logowaniem.

Podczas uwierzytelniania usługa Azure AD wysyła token do aplikacji lub oferty. Informacje o użytkowniku udostępniane przez token umożliwiają utworzenie konta użytkownika w Twojej aplikacji lub ofercie. Aby dowiedzieć się więcej, zobacz [przykładowe tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

W przypadku korzystania z usługi Azure AD w celu włączenia uwierzytelniania jednym kliknięciem na liście aplikacji lub wersji próbnej:

- Usprawnij pracę klienta z portalu Marketplace na liście próbnej.
- Zapoznaj się z działaniem środowiska w produkcie nawet wtedy, gdy użytkownik jest przekierowywany z portalu Marketplace do środowiska domeny lub wersji próbnej.
- Zmniejszenie prawdopodobieństwa porzucenia w przypadku przekierowania użytkowników z powodu braku dodatkowych kroków związanych z logowaniem.
- Ogranicz bariery wdrożenia dla dużej populacji użytkowników usługi Azure AD.

## <a name="verify-azure-ad-integration"></a>Weryfikuj integrację z usługą Azure AD

### <a name="multitenant-solutions"></a>Rozwiązania z wieloma dzierżawcami

Użyj usługi Azure AD, aby obsłużyć następujące akcje:

- Zarejestruj swoją aplikację w jednym z witryny Marketplace. Aby uzyskać więcej informacji, Wyświetl [rejestrację aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) lub [certyfikat AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) .
- Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby skorzystać z wersji próbnej jednego kliknięcia.

Jeśli dopiero zaczynasz korzystać z federacyjnego logowania jednokrotnego usługi Azure AD, wykonaj następujące czynności:

1. Zarejestruj swoją aplikację w portalu Marketplace.
1. Utwórz Logowanie jednokrotne za pomocą usługi Azure AD przy użyciu protokołu [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) lub [OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby udostępnić wersję próbną jednego kliknięcia.

### <a name="single-tenant-solutions"></a>Rozwiązania z jedną dzierżawą

Użyj usługi Azure AD, aby obsłużyć jedną z następujących akcji:

- Dodawanie użytkowników-Gości do katalogu za pomocą [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ręcznie skonfiguruj wersje próbne dla klientów za pomocą opcji publikowania do **kontaktów** .
- Tworzenie dysku testowego dla klienta.
- Utwórz przykładową aplikację demonstracyjną obejmującą wiele dzierżawców, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Marketplace.

Aby zarejestrować się w centrum partnerskim, zacznij tworzyć nową ofertę lub pracować na istniejącym:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
