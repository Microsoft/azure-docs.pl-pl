---
title: Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia używanie Azure Security Center do zarządzania zdarzeniami zabezpieczeń.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652110"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center

Segregowania i badanie alertów zabezpieczeń może być czasochłonne dla nawet najbardziej wykwalifikowanych analityków zabezpieczeń. Dla wielu, trudno jest wiedzieć, gdzie zacząć. 

Security Center używa [analizy](./security-center-alerts-overview.md) do łączenia informacji różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md). Korzystając z tych połączeń, Security Center może zapewnić pojedynczy widok kampanii atakującej i powiązane z nią alerty, aby ułatwić zrozumienie akcji osoby atakującej i odpowiednich zasobów.

Ta strona zawiera omówienie zdarzeń w Security Center.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?

W usłudze Security Center zdarzenie zabezpieczeń to agregacja wszystkich alertów dotyczących zasobu, które są zgodne ze wzorcami ataku cybernetycznego typu [kill chain](alerts-reference.md#intentions). Zdarzenia są wyświetlane na stronie [alerty zabezpieczeń](security-center-managing-and-responding-alerts.md) . Wybierz zdarzenie, aby wyświetlić powiązane alerty i uzyskać więcej informacji.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń

1. Na stronie alerty na Security Center Użyj przycisku **Dodaj filtr** , aby filtrować według nazwy alertu do zdarzenia zabezpieczeń Nazwa alertu **wykrytego dla wielu zasobów**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Lokalizowanie zdarzeń na stronie alertów w Azure Security Center":::

    Lista jest teraz filtrowana, aby pokazać tylko zdarzenia. Zwróć uwagę, że zdarzenia zabezpieczeń mają inną ikonę dla alertów zabezpieczeń.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Lista zdarzeń na stronie Alerty w Azure Security Center":::

1. Aby wyświetlić szczegóły zdarzenia, wybierz jedną z listy. Zostanie wyświetlone okienko boczne zawierające więcej szczegółów dotyczących zdarzenia.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Okienko boczne przedstawiające szczegóły zdarzenia":::

1. Aby wyświetlić więcej szczegółów, wybierz opcję **Wyświetl pełne szczegóły**.

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