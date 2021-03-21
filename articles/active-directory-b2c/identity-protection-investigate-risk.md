---
title: Zbadaj ryzyko przy użyciu programu Azure Active Directory B2C Identity Protection
description: Dowiedz się, jak badać ryzykowne Użytkownicy i wykrywać w usłudze Azure AD B2C Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055702"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Zbadaj ryzyko związane z ochroną tożsamości w Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Program Identity Protection zapewnia stałe wykrywanie ryzyka dla dzierżawy Azure AD B2C. Umożliwia ona organizacjom odnajdywanie, badanie i korygowanie zagrożeń opartych na tożsamościach. Ochrona tożsamości obejmuje raporty o podwyższonym ryzyku, które mogą służyć do badania zagrożeń związanych z tożsamościami w dzierżawach Azure AD B2C. W tym artykule dowiesz się, jak badać i ograniczać ryzyko.

## <a name="overview"></a>Omówienie

Program Azure AD B2C Identity Protection oferuje dwa raporty. Raport dotyczący *ryzykownych użytkowników* polega na tym, że administratorzy mogą znaleźć użytkowników, którzy są zagrożeni i szczegółowe informacje dotyczące wykrywania. Raport *wykrywania ryzyka* zawiera informacje o każdym wykrywaniu ryzyka, w tym o typie, innym ryzyku wyzwalanym w tym samym czasie, lokalizacji próby logowania i innych.

Każdy raport zostanie uruchomiony z listą wszystkich wykrytych wykryć dla okresu wyświetlanego w górnej części raportu. Raporty można filtrować przy użyciu filtrów w górnej części raportu. Administratorzy mogą pobrać dane lub użyć [zestawu SDK programu MS interfejs API programu Graph i Microsoft Graph](../active-directory/identity-protection/howto-identity-protection-graph-api.md) do ciągłego eksportowania danych.

## <a name="service-limitations-and-considerations"></a>Ograniczenia dotyczące usługi i zagadnienia

W przypadku korzystania z usługi Identity Protection należy wziąć pod uwagę następujące kwestie:

- Ochrona tożsamości jest domyślnie włączona.
- Usługi Identity Protection są dostępne zarówno dla tożsamości lokalnych, jak i społecznościowych, takich jak Google lub Facebook. W przypadku tożsamości społecznościowych należy aktywować dostęp warunkowy. Wykrywanie jest ograniczone, ponieważ poświadczenia konta społecznościowego są zarządzane przez zewnętrznego dostawcę tożsamości.
- W dzierżawach Azure AD B2C jest dostępny tylko podzestaw [wykrywania ryzyka Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) . Następujące wykrycia ryzyka są obsługiwane przez Azure AD B2C:  

|Typ wykrywania ryzyka  |Opis  |
|---------|---------|
| Nietypowa podróż     | Zaloguj się z nietypowej lokalizacji na podstawie ostatnich logowań użytkownika.        |
|Anonimowy adres IP     | Logowanie z anonimowego adresu IP (na przykład: przeglądarka tor, Anonymizer sieci VPN).        |
|Połączony adres IP złośliwego oprogramowania     | Logowanie za pomocą połączonego adresu IP złośliwego oprogramowania.         |
|Nieznane właściwości logowania     | Zaloguj się przy użyciu właściwości, które nie były ostatnio widziane dla danego użytkownika.        |
|Administrator zatwierdził naruszenie zabezpieczeń    | Administrator wykrył, że naruszono bezpieczeństwo użytkownika.             |
|Rozpylanie hasła     | Logowanie za pomocą ataku polegającego na rozpylaniu hasła.      |
|Analiza zagrożeń usługi Azure AD     | Wewnętrzne i zewnętrzne źródła analizy zagrożeń firmy Microsoft określiły znany wzorzec ataku.        |

## <a name="pricing-tier"></a>Warstwa cenowa

W przypadku niektórych funkcji ochrony tożsamości jest wymagany Azure AD B2C Premium P2. W razie potrzeby [Zmień warstwę cenową Azure AD B2C na Premium P2](./billing.md). Poniższa tabela zawiera podsumowanie funkcji ochrony tożsamości i wymaganej warstwy cenowej.  

|Cecha   |P1   |P2|
|----------|:-----------:|:------------:|
|Raport ryzykownych użytkowników     |&#x2713; |&#x2713; |
|Szczegóły raportu dotyczącego ryzykownych użytkowników  | |&#x2713; |
|Ryzykowne raporty użytkowników    | &#x2713; |&#x2713; |
|Raport wykrywania ryzyka   |&#x2713;|&#x2713;|
|Szczegóły raportu dotyczącego wykrywania ryzyka  ||&#x2713;|
|Pobieranie raportu |  &#x2713;| &#x2713;|
|Dostęp do programu MS interfejs API programu Graph |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Badanie ryzykownych użytkowników

Z informacjami udostępnianymi przez raport ryzykownych użytkowników Administratorzy mogą znaleźć następujące informacje:

- Którzy użytkownicy podlegają zagrożeniom, mają ryzyko skorygowane lub ryzyko odrzucone?
- Szczegóły dotyczące wykrywania
- Historia wszystkich ryzykownych logowań
- Historia ryzyka
 
Administratorzy mogą następnie podjąć odpowiednie działania dotyczące tych zdarzeń. Administratorzy mogą wybrać następujące opcje:

- Zresetuj hasło użytkownika
- Potwierdzanie naruszenia użytkowników
- Odrzuć ryzyko związane z użytkownikiem
- Zablokuj Logowanie użytkownika
- Zbadaj więcej przy użyciu usługi Azure ATP

### <a name="navigating-the-risky-users-report"></a>Nawigowanie po raportach dotyczących ryzykownych użytkowników

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

1. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.

1. W obszarze **zabezpieczenia** wybierz pozycję **ryzykowni użytkownicy (wersja zapoznawcza)**.

   ![Ryzykowni użytkownicy](media/identity-protection-investigate-risk/risky-users.png)

    Wybranie poszczególnych wpisów rozszerza okno Szczegóły poniżej wykrycia. Widok szczegóły umożliwia administratorom badanie i wykonywanie akcji przy każdym wykryciu.

    ![Akcje podejmowane przez ryzykownych użytkowników](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Raport wykrywania ryzyka

Raport wykrywania ryzyka zawiera dane z możliwością filtrowania do ostatnich 90 dni (trzy miesiące).

Korzystając z informacji podanych w raporcie wykrywania ryzyka, Administratorzy mogą znaleźć następujące informacje:

- Informacje o każdym wykrywaniu ryzyka włącznie z typem.
- Inne zagrożenia wyzwalane w tym samym czasie.
- Lokalizacja próby logowania.

Administratorzy mogą następnie powrócić do raportu o zagrożeniu lub zalogowaniu użytkownika, aby podjąć działania na podstawie zebranych informacji.

### <a name="navigating-the-risk-detections-report"></a>Nawigowanie po raporcie wykrywania ryzyka

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zabezpieczenia** wybierz pozycję **wykrywanie ryzyka (wersja zapoznawcza)**.

   ![Wykrycia ryzyka](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Następne kroki

- [Dodaj dostęp warunkowy do przepływu użytkownika](conditional-access-user-flow.md).