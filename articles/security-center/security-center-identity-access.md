---
title: Zalecenia dotyczące zabezpieczeń Azure Security Center usługi MFA
description: Dowiedz się, jak wymusić uwierzytelnianie wieloskładnikowe dla subskrypcji platformy Azure przy użyciu Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631901"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Zarządzanie wymuszeniem uwierzytelniania wieloskładnikowego (MFA) w Twoich subskrypcjach

Jeśli używasz tylko haseł do uwierzytelniania użytkowników, opuszczasz otwarty wektor ataku. Użytkownicy często używają słabych haseł lub wykorzystują je ponownie dla wielu usług. Gdy usługa [MFA](https://www.microsoft.com/security/business/identity/mfa) jest włączona, Twoje konta są bezpieczniejsze i użytkownicy nadal mogą uwierzytelniać się w prawie każdej aplikacji z logowaniem jednokrotnym (SSO).

Istnieje wiele sposobów włączania uwierzytelniania wieloskładnikowego dla użytkowników usługi Azure Active Directory (AD) na podstawie licencji należących do organizacji. Ta strona zawiera szczegółowe informacje dotyczące każdego z nich w kontekście Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA i Security Center 

Security Center umieszcza dużą wartość na MFA. Kontrola zabezpieczeń, która przyczynia się do najbardziej bezpiecznego oceny, włącza usługę **MFA**. 

Zalecenia zawarte w formancie usługi MFA zapewniają spełnienie zalecanych praktyk dla użytkowników Twoich subskrypcji:

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w subskrypcji

Istnieją trzy sposoby włączania usługi MFA i są zgodne z dwoma zaleceniami w Security Center: domyślne ustawienia zabezpieczeń, przypisywanie dla użytkownika, zasady dostępu warunkowego (CA). Każda z tych opcji została omówiona poniżej.

### <a name="free-option---security-defaults"></a>Opcja bezpłatna — ustawienia domyślne zabezpieczeń
Jeśli używasz bezpłatnej wersji usługi Azure AD, użyj [domyślnych ustawień zabezpieczeń](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) , aby włączyć uwierzytelnianie wieloskładnikowe w dzierżawie.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Uwierzytelnianie wieloskładnikowe dla klientów Microsoft 365 Business, E3 lub E5
Klienci z Microsoft 365 mogą korzystać z **przypisań do poszczególnych użytkowników**. W tym scenariuszu usługa Azure AD MFA jest włączona lub wyłączona dla wszystkich użytkowników w przypadku wszystkich zdarzeń związanych z logowaniem. Nie ma możliwości włączenia uwierzytelniania wieloskładnikowego dla podzbioru użytkowników lub w określonych scenariuszach, a zarządzanie odbywa się za pomocą portalu pakietu Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>Uwierzytelnianie wieloskładnikowe dla klientów Azure AD — wersja Premium
Aby ulepszyć środowisko użytkownika, należy przeprowadzić uaktualnienie do Azure AD — wersja Premium P1 lub P2 dla opcji **zasad dostępu warunkowego (CA)** . Aby skonfigurować zasady urzędu certyfikacji, musisz mieć [uprawnienia dzierżawy Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

Zasady urzędu certyfikacji muszą:
- Wymuś uwierzytelnianie wieloskładnikowe
- Dołącz Microsoft Azure identyfikator aplikacji zarządzania (797f4846-ba00-4fd7-ba43-dac1f8f63013) lub wszystkie aplikacje
- nie Wyklucz identyfikatora aplikacji zarządzania Microsoft Azure

Klienci korzystający z programu **Azure AD — wersja Premium P1** mogą używać urzędu certyfikacji usługi Azure AD w celu monitowania użytkowników o uwierzytelnianie wieloskładnikowe w określonych scenariuszach lub zdarzeniach spełniających wymagania biznesowe. Inne licencje, które obejmują następujące funkcje: Enterprise Mobility + Security E3, Microsoft 365 F1 i Microsoft 365 E3.

**Azure AD — wersja Premium P2** zapewnia najmocniejsze funkcje zabezpieczeń i udoskonalone środowisko użytkownika. W tej licencji dodano [dostęp warunkowy oparty na ryzyku](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) do funkcji Azure AD — wersja Premium P1. Urząd certyfikacji oparty na ryzyku dostosowuje się do wzorców użytkowników i minimalizuje komunikaty uwierzytelniania wieloskładnikowego. Inne licencje, które obejmują tę funkcję: Enterprise Mobility + Security E5 lub Microsoft 365 E5.

Dowiedz się więcej z [dokumentacji dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identyfikuj konta bez włączonej usługi uwierzytelniania wieloskładnikowego (MFA)

Listę kont użytkowników bez uwierzytelniania wieloskładnikowego można wyświetlić na stronie Szczegóły zaleceń Security Center lub przy użyciu usługi Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Wyświetlanie kont bez włączonej usługi MFA w Azure Portal
Na stronie Szczegóły zalecenia wybierz subskrypcję z listy zasobów w **złej kondycji** lub wybierz pozycję **podejmij akcję** i Wyświetl listę.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Wyświetlanie kont bez włączonego uwierzytelniania wieloskładnikowego przy użyciu grafu zasobów platformy Azure
Aby sprawdzić, które konta nie mają włączonej usługi MFA, użyj następującego zapytania do wykresu zasobów platformy Azure. Zapytanie zwraca wszystkie zasoby o złej kondycji — konta — zalecenie "MFA należy włączyć na kontach z uprawnieniami właściciela w Twojej subskrypcji". 

1. Otwórz **Eksploratora Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Uruchamianie Eksploratora Azure Resource Graph * * zalecenie" :::

1. Wprowadź następujące zapytanie i wybierz polecenie **Uruchom zapytanie**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`Właściwość pokazuje listę identyfikatorów obiektów kont dla kont, które nie mają wymuszonego uwierzytelniania wieloskładnikowego. 

    > [!NOTE]
    > Konta są wyświetlane jako identyfikatory obiektów zamiast nazw kont w celu ochrony prywatności posiadaczy kont.

> [!TIP]
> Alternatywnie można użyć oceny metody interfejsu API REST Security Center [— Get](/rest/api/securitycenter/assessments/get).


## <a name="faq---mfa-in-security-center"></a>Często zadawane pytania — MFA w Security Center

- [Korzystamy już z zasad urzędu certyfikacji, aby wymusić uwierzytelnianie MFA. Dlaczego nadal Security Center rekomendacje?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Korzystamy z narzędzia MFA innej firmy w celu wymuszenia uwierzytelniania MFA. Dlaczego nadal Security Center rekomendacje?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Dlaczego Security Center pokazać konta użytkowników bez uprawnień do subskrypcji jako "wymaganie MFA"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Wymuszamy uwierzytelnianie wieloskładnikowe za pomocą usługi PIM. Dlaczego konta PIM są wyświetlane jako niezgodne?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Czy można wykluczać lub odrzucać niektóre konta?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Czy istnieją jakieś ograniczenia dotyczące tożsamości i ochrony dostępu Security Centeru?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Korzystamy już z zasad urzędu certyfikacji, aby wymusić uwierzytelnianie MFA. Dlaczego nadal Security Center rekomendacje?
Aby sprawdzić, dlaczego nadal są generowane zalecenia, sprawdź następujące opcje konfiguracji w zasadach urzędu certyfikacji MFA:

- Konta zostały uwzględnione w sekcji **Użytkownicy** zasad urzędu certyfikacji MFA (lub jednej z grup w sekcji **grupy** ).
- Identyfikator aplikacji zarządzania Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) lub wszystkie aplikacje są zawarte w sekcji **aplikacje** zasad urzędu certyfikacji MFA
- Identyfikator aplikacji zarządzania platformy Azure nie jest wykluczony w sekcji **aplikacje** zasad urzędu certyfikacji MFA

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Korzystamy z narzędzia MFA innej firmy w celu wymuszenia uwierzytelniania MFA. Dlaczego nadal Security Center rekomendacje?
Zalecenia dotyczące usługi MFA Security Center nie obsługują narzędzi MFA innych firm (na przykład DUO).

Jeśli rekomendacje nie są istotne dla organizacji, rozważ oznaczenie ich jako "skorygowane" zgodnie z opisem w temacie [zwalnianie zasobów i zaleceń z bezpiecznego wyniku](exempt-resource.md). Możesz również [wyłączyć zalecenia](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Dlaczego Security Center pokazać konta użytkowników bez uprawnień do subskrypcji jako "wymaganie MFA"?
Zalecenia dotyczące usługi MFA Security Center dotyczą ról [RBAC platformy Azure](../role-based-access-control/role-definitions-list.md) i roli [administratorów klasycznych subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md) . Sprawdź, czy żadne z kont nie ma takich ról.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Wymuszamy uwierzytelnianie wieloskładnikowe za pomocą usługi PIM. Dlaczego konta PIM są wyświetlane jako niezgodne?
Zalecenia dotyczące usługi MFA Security Center obecnie nie obsługują kont PIM. Te konta można dodać do zasad urzędu certyfikacji w sekcji Użytkownicy/Grupa.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Czy można wykluczać lub odrzucać niektóre konta?
Możliwość wykluczenia niektórych kont, które nie korzystają z usługi MFA, nie jest obecnie obsługiwana.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Czy istnieją jakieś ograniczenia dotyczące tożsamości i ochrony dostępu Security Centeru?
Istnieją pewne ograniczenia dotyczące tożsamości i ochrony dostępu Security Center:

- Zalecenia dotyczące tożsamości nie są dostępne dla subskrypcji mających więcej niż 600 kont. W takich przypadkach te zalecenia będą wyświetlane w obszarze "niedostępne oceny".
- Zalecenia dotyczące tożsamości nie są dostępne dla agentów administratora dostawcy rozwiązań w chmurze (CSP).
- Zalecenia dotyczące tożsamości nie identyfikują kont zarządzanych przy użyciu systemu Privileged Identity Management (PIM). Jeśli używasz narzędzia PIM, możesz zobaczyć niedokładne wyniki w formancie **Zarządzanie dostępem i uprawnieniami** .


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujący artykuł:

- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)