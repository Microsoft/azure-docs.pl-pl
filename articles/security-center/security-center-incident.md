---
title: Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia używanie Azure Security Center do zarządzania zdarzeniami zabezpieczeń.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 2b5dc30de19704b5e8950515cfa6224b4bbdbaf0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341350"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center

Segregowania i badanie alertów zabezpieczeń może być czasochłonne dla nawet najbardziej wykwalifikowanych analityków zabezpieczeń. Dla wielu, trudno jest wiedzieć, gdzie zacząć. 

Security Center używa [analizy](./security-center-alerts-overview.md) do łączenia informacji różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md). Korzystając z tych połączeń, Security Center może zapewnić pojedynczy widok kampanii atakującej i powiązane z nią alerty, aby ułatwić zrozumienie akcji osoby atakującej i odpowiednich zasobów.

Ta strona zawiera omówienie zdarzeń w Security Center.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?

W usłudze Security Center zdarzenie zabezpieczeń to agregacja wszystkich alertów dotyczących zasobu, które są zgodne ze wzorcami ataku cybernetycznego typu [kill chain](alerts-reference.md#intentions). Zdarzenia są wyświetlane na stronie [alerty zabezpieczeń](security-center-managing-and-responding-alerts.md) . Wybierz zdarzenie, aby wyświetlić powiązane alerty i uzyskać więcej informacji.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń

1. Na stronie Przegląd Security Center wybierz kafelek **alerty zabezpieczeń** . Zdarzenia i alerty są wymienione na liście. Zwróć uwagę, że zdarzenia zabezpieczeń mają inną ikonę dla alertów zabezpieczeń.

    ![Wyświetl zdarzenia dotyczące zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby wyświetlić szczegóły, wybierz zdarzenie. Strona **incydentu zabezpieczeń** zawiera więcej szczegółów. 

    [![Reagowanie na zdarzenia związane z bezpieczeństwem w Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    W lewym okienku na stronie zdarzenia zabezpieczeń są wyświetlane ogólne informacje o zdarzeniu zabezpieczeń: tytuł, ważność, stan, czas działania, opis i zasób, którego dotyczy problem. Obok zasobu, którego dotyczy problem, można zobaczyć odpowiednie Tagi platformy Azure. Użyj tych tagów do wywnioskowania kontekstu organizacyjnego zasobu podczas badania alertu.

    Prawe okienko zawiera kartę **alerty** z alertami zabezpieczeń, które zostały skorelowane jako część tego zdarzenia. 

    >[!TIP]
    > Aby uzyskać więcej informacji na temat określonego alertu, wybierz go. 

    [![Karta akcja podjęcie zdarzenia](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Aby przełączyć się na kartę **Wypełnij akcję** , wybierz kartę lub przycisk w dolnej części okienka po prawej stronie. Ta karta umożliwia podejmowanie dalszych działań, takich jak:
    - *Eliminowanie zagrożeń* — zawiera ręczne kroki korygowania dla tego zdarzenia zabezpieczeń
    - *Zapobiegaj atakom w przyszłości* — zawiera zalecenia dotyczące zabezpieczeń, które pomagają w zmniejszeniu podatności na ataki, zwiększyć bezpieczeństwo stan i zapobiec przyszłym atakom
    - *Wyzwalanie automatycznej odpowiedzi* — udostępnia opcję wyzwalania aplikacji logiki jako odpowiedzi na to zdarzenie zabezpieczeń
    - *Pomijaj podobne alerty* — oferuje opcję pomijania przyszłych alertów o podobnych cechach, jeśli alert nie jest odpowiedni dla Twojej organizacji. 

   > [!NOTE]
   > Ten sam alert może istnieć jako część zdarzenia, a także być widoczny jako alert autonomiczny.

1. Aby skorygować zagrożenia w zdarzeniu, postępuj zgodnie z krokami korygującymi podanymi dla każdego alertu.


## <a name="next-steps"></a>Następne kroki

Na tej stronie objaśniono możliwości zdarzeń zabezpieczeń Security Center. Aby uzyskać powiązane informacje, zobacz następujące strony:

- [Alerty zabezpieczeń w Security Center](security-center-alerts-overview.md)
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie](security-center-managing-and-responding-alerts.md)