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
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 042780c313c444062fd512ab0d9f38aaeb6cf170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894564"
---
# <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu

> [!TIP]
> Od marca 2020 zalecenia dotyczące tożsamości i dostępu Azure Security Center są zawarte we wszystkich subskrypcjach w warstwie cenowej bezpłatna. Jeśli masz subskrypcje w warstwie Bezpłatna, ich bezpieczny wynik będzie miał oddziaływać na to, co nie oceniono wcześniej pod kątem ich tożsamości i zabezpieczeń dostępu. 

Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

Obwód zabezpieczeń został rozwijający się od obwodu sieci do obwodu tożsamości. Zabezpieczenia są mniej dotyczące obrony sieci i więcej informacji na temat obrony danych, a także do zarządzania zabezpieczeniami aplikacji i użytkowników. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Monitorując działania związane z tożsamościami, możesz podejmować aktywne akcje przed wystąpieniem zdarzenia lub ponownie uaktywnić akcje, aby przerwać próbę ataku. Na przykład Security Center mogą oflagować przestarzałe konta (konta, które nie są już potrzebne, i zablokować możliwość logowania się przez Azure Active Directory) do usunięcia. 

Przykłady zaleceń, które mogą być widoczne w sekcji zabezpieczenia dotyczące **tożsamości i dostępu do** zasobów Azure Security Center obejmują:

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Przestarzałe konta powinny zostać usunięte z subskrypcji

Aby uzyskać więcej informacji na temat tych zaleceń, a także pełną listę zaleceń, które mogą pojawić się w tym miejscu, zobacz [zalecenia dotyczące tożsamości i dostępu](recommendations-reference.md#recs-identity).

> [!NOTE]
> Jeśli subskrypcja ma więcej niż 600 kont, Security Center nie może uruchomić zaleceń dotyczących tożsamości dla subskrypcji. Zalecenia, które nie są uruchamiane, znajdują się w obszarze "oceny niedostępne" poniżej.
Security Center nie może uruchomić zaleceń dotyczących tożsamości dla agentów administratora dostawcy rozwiązań w chmurze (CSP).
>


Wszystkie zalecenia dotyczące tożsamości i dostępu są dostępne w ramach dwóch kontrolek zabezpieczeń na stronie **zalecenia** :

- Zarządzanie dostępem i uprawnieniami 
- Włączanie usługi MFA

![Dwie kontrole zabezpieczeń z zaleceniami dotyczącymi tożsamości i dostępu](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Włączanie uwierzytelniania wieloskładnikowego (MFA)

Włączenie usługi MFA wymaga [uprawnień dzierżawy Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Jeśli masz wersję Premium usługi AD, Włącz uwierzytelnianie wieloskładnikowe przy użyciu [dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md).

- Jeśli używasz bezpłatnej wersji usługi AD, Włącz **domyślne ustawienia zabezpieczeń** w Azure Active Directory zgodnie z opisem w [dokumentacji usługi AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)