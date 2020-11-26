---
title: Monitorowanie tożsamości i dostępu w usłudze Azure Security Center | Microsoft Docs
description: Dowiedz się, jak korzystać z funkcji zarządzania tożsamościami i dostępem w usłudze Azure Security Center w celu monitorowania dostępu użytkowników i rozwiązywania problemów związanych z tożsamościami.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: b36f52fd97a9f7ff294f14ec394ff9a9f607b5b0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187157"
---
# <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu

Obwód zabezpieczeń ewoluował od obwodu sieci do obwodu tożsamości. W przypadku tego rozwoju zabezpieczenia są mniej związane z obroną sieci oraz więcej informacji na temat zarządzania zabezpieczeniami aplikacji, danych i użytkowników.

Monitorując działania i ustawienia konfiguracji związane z tożsamością, możesz podejmować aktywne akcje przed wystąpieniem zdarzenia lub ponownie uaktywnić akcje, aby przerwać próby ataków.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Jakie zabezpieczenia dotyczące tożsamości i dostępu zapewniają Security Center? 

Azure Security Center ma dwie dedykowane mechanizmy kontroli zabezpieczeń, aby upewnić się, że spełniasz wymagania dotyczące tożsamości i zabezpieczeń organizacji: 

 - **Zarządzanie dostępem i uprawnieniami** — zachęcamy do zastosowania [modelu dostępu o najniższym poziomie uprawnień](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i upewnienia się, że użytkownicy będą mieli tylko dostęp niezbędny do wykonywania swoich zadań. Ta kontrolka zawiera również zalecenia dotyczące implementowania [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) w celu kontrolowania dostępu do zasobów.
 
 - **Włączenie uwierzytelniania wieloskładnikowego** — z włączoną funkcją [MFA](https://www.microsoft.com/security/business/identity/mfa) konta są bezpieczniejsze, a użytkownicy nadal mogą uwierzytelniać się w prawie każdej aplikacji z logowaniem jednokrotnym.

### <a name="example-recommendations-for-identity-and-access"></a>Przykładowe zalecenia dotyczące tożsamości i dostępu

Przykłady zaleceń, które mogą pojawić się w tych dwóch kontrolkach na stronie **zalecenia** dotyczące Security Center:

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Przestarzałe konta należy usunąć z subskrypcji (konta przestarzałe to konta, które nie są już potrzebne, i zablokowany do logowania się przez Azure Active Directory)

> [!TIP]
> Aby uzyskać więcej informacji na temat tych zaleceń i innych osób, które mogą się pojawić w tych kontrolkach, zobacz [zalecenia dotyczące tożsamości i dostępu](recommendations-reference.md#recs-identity).

### <a name="limitations"></a>Ograniczenia

Istnieją pewne ograniczenia dotyczące tożsamości i ochrony dostępu Security Center:

- Zalecenia dotyczące tożsamości nie są dostępne dla subskrypcji mających więcej niż 600 kont. W takich przypadkach te zalecenia będą wyświetlane w obszarze "niedostępne oceny".
- Zalecenia dotyczące tożsamości nie są dostępne dla agentów administratora dostawcy rozwiązań w chmurze (CSP).
- Zalecenia dotyczące tożsamości nie identyfikują kont zarządzanych przy użyciu systemu Privileged Identity Management (PIM). Jeśli używasz narzędzia PIM, możesz zobaczyć niedokładne wyniki w formancie **Zarządzanie dostępem i uprawnieniami** .

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Uwierzytelnianie wieloskładnikowe (MFA) i Azure Active Directory 

Włączenie usługi MFA wymaga [uprawnień dzierżawy Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

- Jeśli masz wersję Premium usługi AD, Włącz uwierzytelnianie wieloskładnikowe przy użyciu [dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Jeśli używasz bezpłatnej wersji usługi AD, Włącz **domyślne ustawienia zabezpieczeń** zgodnie z opisem w [dokumentacji Azure Active Directory](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identyfikuj konta bez włączonej usługi uwierzytelniania wieloskładnikowego (MFA)

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


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujący artykuł:

- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)