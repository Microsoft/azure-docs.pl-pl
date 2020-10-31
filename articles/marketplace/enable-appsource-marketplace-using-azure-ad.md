---
title: Integruj swoją ofertę komercyjnej witryny Microsoft Marketplace z Azure Active Directory
description: Użyj Azure Active Directory do uwierzytelniania Microsoft AppSource i ofert w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: c70d976a05cbeed058243829d0658693341636e9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131211"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Zintegruj swoją aukcję komercyjną Marketplace z usługą Azure Active Directory

 W tym artykule objaśniono wymagania dotyczące integrowania komercyjnej oferty rynkowej z usługą Azure Active Directory (Azure AD). Azure AD to usługa tożsamości w chmurze, która używa standardów branżowych, aby umożliwić uwierzytelnianie za pomocą konto Microsoft. [Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Klienci korzystający z usług Microsoft AppSource i Azure Marketplace wykorzystują środowiska w ramach produktu, aby przeszukiwać katalogi z listą sklepu online. Te akcje wymagają od klientów zalogowania się do produktu. Integracja z usługą Azure AD zapewnia następujące korzyści:

- Szybsza zaangażowanie i zoptymalizowane środowisko klienta
- Logowanie jednokrotne (SSO) dla milionów użytkowników w przedsiębiorstwach
- Spójne środowisko logowania między aplikacjami opublikowanymi przez różnych partnerów
- Skalowalne, międzyplatformowe uwierzytelnianie dla aplikacji mobilnych i w chmurze

## <a name="offers-that-require-azure-ad"></a>Oferty wymagające usługi Azure AD

Różne [Opcje i typy ofert](determine-your-listing-type.md) dostępne w komercyjnej witrynie Marketplace mają różne wymagania dotyczące implementacji usługi Azure AD. Szczegółowe informacje znajdują się w poniższej tabeli.

| Typ oferty    | Logowanie jednokrotne usługi Azure AD jest wymagane do skontaktowania się ze mną  | Usługa Azure AD SSO jest wymagana w przypadku wersji próbnej? | Usługa Azure AD SSO jest wymagana dla dysku testowego?  | Logowanie jednokrotne usługi Azure AD jest wymagane dla języka Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Maszyna wirtualna | Nie dotyczy | Nie | Nie | Nie |
| Aplikacje platformy Azure (szablon rozwiązania)  | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |
| Aplikacje zarządzane  | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | Nie |
| SaaS  | Nie | Tak | Tak | Tak |
| Kontenery  | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | Nie |
| Usługi konsultingowe  | Nie | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |

Aby uzyskać więcej informacji o wymaganiach technicznych SaaS, zobacz [oferty usługi Azure AD i transacting SaaS w portalu komercyjnym](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

- Aby uzyskać szczegółowe informacje na temat sposobu integrowania usługi Azure AD z ofertami SaaS (Software as a Service), zobacz [oferty usługi Azure AD i transacting SaaS w portalu komercyjnym](./azure-ad-saas.md).
- Aby uzyskać informacje na temat włączania logowania jednokrotnego przez integrację usługi Azure AD z listą, zobacz [Azure Active Directory dla deweloperów](../active-directory/develop/index.yml).
- Aby uzyskać szczegółowe informacje na temat logowania jednokrotnego w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Włącz listę próbną

Zautomatyzowana konfiguracja klienta może zwiększyć prawdopodobieństwo konwersji. Gdy klient wybierze swoją listę próbną i zostanie przekierowany do środowiska w wersji próbnej, można skonfigurować klienta bezpośrednio bez konieczności wykonywania dodatkowych czynności związanych z logowaniem.

Podczas uwierzytelniania usługa Azure AD wysyła token do aplikacji lub oferty. Informacje o użytkowniku udostępniane przez token umożliwiają utworzenie konta użytkownika w Twojej aplikacji lub ofercie. Aby dowiedzieć się więcej, zobacz [przykładowe tokeny](../active-directory/develop/id-tokens.md).

W przypadku korzystania z usługi Azure AD w celu włączenia uwierzytelniania jednym kliknięciem na liście aplikacji lub wersji próbnej:

- Usprawnij pracę klienta z komercyjnej witryny Marketplace na liście próbnej.
- Zapoznaj się z działaniem środowiska w produkcie nawet wtedy, gdy użytkownik jest przekierowywany z komercyjnej witryny Marketplace do domeny lub środowiska próbnego.
- Zmniejszenie prawdopodobieństwa porzucenia w przypadku przekierowania użytkowników z powodu braku dodatkowych kroków związanych z logowaniem.
- Ogranicz bariery wdrożenia dla dużej populacji użytkowników usługi Azure AD.

## <a name="verify-azure-ad-integration"></a>Weryfikuj integrację z usługą Azure AD

### <a name="multitenant-solutions"></a>Rozwiązania z wieloma dzierżawcami

Użyj usługi Azure AD, aby obsłużyć następujące akcje:

- Zarejestruj swoją aplikację w jednym z komercyjnych sklepów online Marketplace. Aby uzyskać więcej informacji, Wyświetl [rejestrację aplikacji](../active-directory/develop/quickstart-register-app.md) lub [certyfikat AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md) .
- Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby skorzystać z wersji próbnej jednego kliknięcia.

Jeśli dopiero zaczynasz korzystać z federacyjnego logowania jednokrotnego usługi Azure AD, wykonaj następujące czynności:

1. Zarejestruj swoją aplikację w komercyjnym portalu Marketplace.
1. Utwórz Logowanie jednokrotne za pomocą usługi Azure AD przy użyciu protokołu [OAuth 2,0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) lub [OpenID Connect Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby udostępnić wersję próbną jednego kliknięcia.

### <a name="single-tenant-solutions"></a>Rozwiązania z jedną dzierżawą

Użyj usługi Azure AD, aby obsłużyć jedną z następujących akcji:

- Dodawanie użytkowników-Gości do katalogu za pomocą [usługi Azure AD B2B](../active-directory/external-identities/what-is-b2b.md).
- Ręcznie skonfiguruj wersje próbne dla klientów za pomocą opcji publikowania do **kontaktów** .
- Tworzenie dysku testowego dla klienta.
- Utwórz przykładową aplikację demonstracyjną obejmującą wiele dzierżawców, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o komercyjnym rynku.

Aby zarejestrować się w centrum partnerskim, zacznij tworzyć nową ofertę lub pracować na istniejącym:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.