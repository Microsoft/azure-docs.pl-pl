---
title: Włączanie usługi Microsoft AppSource i usługi Azure Marketplace przy użyciu usługi Azure Active Directory | Azure
description: Włącz typ aukcji przy użyciu usługi Azure Active Directory w portalu Azure Marketplace i AppSource dla wydawców aplikacji i usług.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: d2f33fc8b1bdd15b53ad9130b5cc598dd6d5ee1a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684624"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Włączanie możliwości publikowania ofert w witrynach AppSource i Marketplace z użyciem usługi Azure Active Directory

 Usługa Azure Active Directory (Azure AD) to usługa tożsamości w chmurze, która umożliwia uwierzytelnianie za pomocą konta Microsoft. Usługa Azure AD korzysta ze standardowych struktur branżowych. [Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Klienci korzystający z usługi Microsoft AppSource i azure marketplace korzystają z doświadczeń w produktach do przeszukiwania katalogów aukcji. Te akcje wymagają od klientów zalogowania się do produktu. Integracja z usługą Azure AD zapewnia następujące korzyści:

- Szybsze zaangażowanie i zoptymalizowane środowisko klienta
- Logowanie jednokrotne (Logowanie jednokrotne) dla milionów użytkowników korporacyjnych
- Spójne, logowania w aplikacjach publikowanych przez różnych partnerów
- Skalowalne uwierzytelnianie między platformami dla aplikacji mobilnych i w chmurze

## <a name="offers-that-require-azure-ad"></a>Oferty wymagające usługi Azure AD

Różne [opcje wyświetlania i typy ofert](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) dla usługi AppSource i Azure Marketplace mają różne wymagania dotyczące implementacji usługi Azure AD. Szczegółowe informacje można znaleźć w poniższej tabeli:

| **Typ oferty**    | **Wymagane jest przysłów y sytuane usługi Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Skontaktuj się ze mną | Wersja próbna | Wersja testowa | Transact |
| Maszyna wirtualna | Nie dotyczy | Nie | Nie | Nie |
| Usługa Azure Apps (szablon rozwiązania)  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| Aplikacje zarządzane  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie |
| SaaS  | Nie | Tak | Tak | Tak |
| Containers  | Nie dotyczy | Nie dotyczy | Nie dotyczy | Nie |
| Usługi konsultingowe  | Nie | Nie dotyczy | Nie dotyczy | Nie dotyczy |

Aby uzyskać więcej informacji na temat wymagań technicznych SaaS, zobacz [SaaS aplikacje Oferta Publishing Guide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integracja usługi Azure AD

- Aby uzyskać informacje na temat włączania logowania jednokrotnego przez integrowanie usługi Azure AD z aukcją, zobacz [Usługa Azure Active Directory dla deweloperów]( https://aka.ms/aaddev).
- Aby uzyskać szczegółowe informacje na temat logowania jednokrotnego usługi Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Włączanie listy próbnej

Zautomatyzowana konfiguracja klienta może zwiększyć prawdopodobieństwo konwersji. Gdy klient wybierze listę wersji próbnej i zostanie przekierowany do środowiska próbnego, możesz skonfigurować klienta bezpośrednio bez konieczności dodatkowych kroków logowania.

Podczas uwierzytelniania usługa Azure AD wysyła token do aplikacji lub oferty. Informacje o użytkowniku dostarczane przez token umożliwia utworzenie konta użytkownika w aplikacji lub oferty. Aby dowiedzieć się więcej, zobacz [Przykładowe tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Korzystając z usługi Azure AD w celu włączenia uwierzytelniania jednym kliknięciem w aplikacji lub na liście próbnej, możesz:

- Usprawnij obsługę klienta z Marketplace do listy próbnej.
- Zachowaj działanie środowiska w produkcie, nawet gdy użytkownik jest przekierowywał z Portalu Marketplace do domeny lub środowiska próbnego.
- Zmniejsz prawdopodobieństwo porzucenia, gdy użytkownicy są przekierowywani, ponieważ nie ma żadnych dodatkowych kroków logowania.
- Zmniejsz bariery wdrażania dla dużej populacji użytkowników usługi Azure AD.

## <a name="verify-azure-ad-integration"></a>Weryfikowanie integracji usługi Azure AD

### <a name="multitenant-solutions"></a>Rozwiązania wielodostępne

Użyj usługi Azure AD do obsługi następujących akcji:

- Zarejestruj aplikację w jednej z witryn sklepowych Marketplace. Wyświetl [rejestrację aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) lub [certyfikat AppSource, aby](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) uzyskać więcej informacji.
- Włącz funkcję obsługi wielu funkcji w usłudze Azure AD, aby uzyskać wersję próbną jednym kliknięciem.

Jeśli jesteś nowy w użyciu usługi Azure AD federacyjne logowania jednokrotnego, wykonaj następujące kroki:

1. Zarejestruj aplikację w Marketplace.
1. Tworzenie jedno i tak z usługą Azure AD przy użyciu [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) lub [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Włącz funkcję obsługi wielu funkcji w usłudze Azure AD, aby zapewnić środowisko próbne jednym kliknięciem.

### <a name="single-tenant-solutions"></a>Rozwiązania dla jednej dzierżawy

Użyj usługi Azure AD do obsługi jednej z następujących akcji:

- Dodawanie użytkowników-gości do katalogu przy użyciu [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ręcznie skonfiguruj wersje próbne dla klientów za pomocą opcji publikowania skontaktuj się z **nami.**
- Opracowanie jazdy testowej dla klienta.
- Tworzenie przykładowej aplikacji demonstracyjnej z wieloma dzierżawami, która używa identyfikatora SSO.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów, rozpocznij tworzenie nowej oferty lub pracę nad istniejącą:

- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
