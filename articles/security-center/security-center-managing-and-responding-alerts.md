---
title: Zarządzanie alertami zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: a47ece762f2df3fa18cf2b79d338c28d4069c4ad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633495"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center

W tym temacie pokazano, jak wyświetlać i przetwarzać alerty Security Center i chronić zasoby.

Zaawansowane wykrywanie wyzwalające alerty zabezpieczeń są dostępne tylko w usłudze Azure Defender. Dostępna jest bezpłatna wersja próbna. Aby przeprowadzić uaktualnienie, zobacz [Włączanie usługi Azure Defender](security-center-pricing.md#enable-azure-defender).

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

Aby dowiedzieć się więcej o różnych typach alertów, zobacz [alerty zabezpieczeń — Przewodnik referencyjny](alerts-reference.md).

Aby zapoznać się z omówieniem Security Center generowania alertów, zobacz [jak Azure Security Center wykrywać zagrożenia i reagować na](security-center-alerts-overview.md)nie.


## <a name="manage-your-security-alerts"></a>Zarządzanie alertami zabezpieczeń

1. Na stronie Przegląd Security Center wybierz kafelek **alerty zabezpieczeń** w górnej części strony lub łącze z paska bocznego.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Uzyskiwanie informacji na stronie alertów zabezpieczeń na stronie przeglądu Azure Security Center":::

    Zostanie otwarta strona alerty zabezpieczeń.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista alertów zabezpieczeń Azure Security Center":::

1. Aby odfiltrować listę alertów, wybierz dowolny z odpowiednich filtrów. Opcjonalnie możesz dodać dalsze filtry przy użyciu opcji **Dodaj filtr** .

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Dodawanie filtrów do widoku alertów" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    Lista jest aktualizowana zgodnie z wybranymi opcjami filtrowania. Filtrowanie może być bardzo przydatne. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.


## <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń

1. Z listy **alerty zabezpieczeń** wybierz alert. Zostanie otwarte okienko boczne zawierające opis alertu i wszystkie zasoby, których to dotyczy. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Widok informacji o minimalnej liczbie alertów zabezpieczeń":::

    > [!TIP]
    > Po otwarciu tego okienka bocznego można szybko przejrzeć listę alertów za pomocą strzałek w górę i w dół na klawiaturze.

1. Aby uzyskać więcej informacji, wybierz opcję **Wyświetl pełne szczegóły**.

    W lewym okienku na stronie alertu zabezpieczeń znajdują się ogólne informacje dotyczące alertu zabezpieczeń: tytuł, ważność, stan, czas działania, opis podejrzanego działania oraz zaatakowany zasób. Obok zasobu, którego dotyczy problem, są Tagi platformy Azure istotne dla zasobu. Użyj tych elementów do wywnioskowania kontekstu organizacyjnego zasobu podczas badania alertu.

    Prawe okienko zawiera kartę **szczegóły alertu** zawierającą dalsze szczegóły alertu, który ułatwia badanie problemu: adresów IP, plików, procesów i nie tylko.
     
    ![Sugestie dotyczące czynności, które należy wykonać, aby uzyskać informacje o alertach zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Również w okienku po prawej stronie znajduje się karta **podjęcie akcji** . Za pomocą tej karty można podejmować dalsze działania dotyczące alertu zabezpieczeń. Akcje, takie jak:
    - *Eliminowanie zagrożeń* — zawiera ręczne kroki korygowania dla tego alertu zabezpieczeń
    - *Zapobiegaj atakom w przyszłości* — zawiera zalecenia dotyczące zabezpieczeń, które pomagają w zmniejszeniu podatności na ataki, zwiększyć bezpieczeństwo stan i w ten sposób zapobiec atakom w przyszłości
    - *Wyzwalanie automatycznej odpowiedzi* — udostępnia opcję wyzwalania aplikacji logiki jako odpowiedzi na ten alert zabezpieczeń
    - *Pomijaj podobne alerty* — oferuje opcję pomijania przyszłych alertów o podobnych cechach, jeśli alert nie jest odpowiedni dla Twojej organizacji.

    ![Przejmij kartę z akcją](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Zobacz też

W tym dokumencie przedstawiono sposób wyświetlania alertów zabezpieczeń. Pokrewnych materiałów można znaleźć na następujących stronach:

- [Konfigurowanie reguł pomijania alertów](alerts-suppression-rules.md)
- [Automatyzowanie odpowiedzi na wyzwalacze Security Center](workflow-automation.md)
- [Alerty zabezpieczeń — przewodnik informacyjny](alerts-reference.md)
