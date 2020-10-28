---
title: Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure
description: W tym dokumencie przedstawiono sposób, w jaki zalecenia w Azure Security Center pomagają chronić zasoby platformy Azure i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: c0952e0f267f9c2511a2e80fa7106958d8dd0ee5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791889"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlać i zrozumieć zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.


## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?

Zalecenia to akcje, które należy wykonać, aby zabezpieczyć zasoby.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Każde zalecenie oferuje następujące informacje:

- Krótki opis problemu
- Czynności zaradcze, które należy wykonać w celu wdrożenia zalecenia
- Zasoby, których to dotyczy

## <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania <a name="monitor-recommendations"></a>

Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. 

1. W menu Security Center Otwórz stronę **zalecenia** , aby zobaczyć zalecenia dotyczące danego środowiska. Zalecenia są pogrupowane w zabezpieczeniach.

      ![Zalecenia pogrupowane według kontroli zabezpieczeń](./media/security-center-recommendations/view-recommendations.png)

1. Rozwiń formant i wybierz konkretne zalecenie, aby wyświetlić stronę szczegóły rekomendacji.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Strona szczegółów rekomendacji." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Strona zawiera następujące:

    1. Przyciski **Wymuś** i **Odmów** w przypadku obsługiwanych zaleceń (zobacz zapobieganie błędom [konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md))
    1. **Wskaźnik ważności**
    1. **Interwał Aktualności**  (jeśli dotyczy) 
    1. **Opis** — Krótki opis problemu
    1. **Kroki zaradcze** — opis ręcznych kroków wymaganych do skorygowania problemu z zabezpieczeniami odpowiednich zasobów. W przypadku rekomendacji z opcją "szybkie rozwiązanie" można wybrać opcję **Wyświetl logikę korygowania** przed zastosowaniem sugerowanej poprawki do zasobów. 
    1. **Zasoby, których to dotyczy** — zasoby są pogrupowane na karty:
        - **Zasoby w dobrej kondycji** — odpowiednie zasoby, na które nie ma wpływu lub na które rozwiązanie problemu zostało już skorygowane.
        - **Zasoby w złej kondycji** — zasoby, na które nadal mają wpływ zidentyfikowane problemy.
        - **Nie dotyczy zasobów** — zasoby, dla których zalecenie nie może udzielić ostatecznej odpowiedzi. Karta nie dotyczy również zawiera przyczyny dla każdego zasobu. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Strona szczegółów rekomendacji.":::
    1. Przyciski akcji do korygowania zalecenia lub wyzwalania aplikacji logiki.

## <a name="preview-recommendations"></a>Zalecenia dotyczące wersji zapoznawczej

Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku.

Powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, więc po zakończeniu okresu korzystania z wersji zapoznawczej będą one wchodzić w skład Twojego wyniku.

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Strona szczegółów rekomendacji.":::
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w Security Center. Aby uzyskać informacje pokrewne:

- [Skoryguj zalecenia](security-center-remediate-recommendations.md)— Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zapobiegaj błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).
- [Automatyzowanie odpowiedzi na Security Center wyzwalacze](workflow-automation.md)— Automatyzowanie odpowiedzi na zalecenia
- [Wykluczanie zasobu z rekomendacji](exempt-resource.md)
- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](recommendations-reference.md)