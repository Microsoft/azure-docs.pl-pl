---
title: Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure
description: W tym dokumencie przedstawiono sposób, w jaki zalecenia w Azure Security Center pomagają chronić zasoby platformy Azure i zachować zgodność z zasadami zabezpieczeń.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100342"
---
# <a name="review-your-security-recommendations"></a>Zapoznaj się z zaleceniami dotyczącymi zabezpieczeń

W tym temacie wyjaśniono, jak wyświetlać i zrozumieć zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

## <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania <a name="monitor-recommendations"></a>

Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. 

1. W menu Security Center Otwórz stronę **zalecenia** , aby zobaczyć zalecenia dotyczące danego środowiska. Zalecenia są pogrupowane w zabezpieczeniach.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Zalecenia pogrupowane według kontroli zabezpieczeń" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Aby znaleźć zalecenia dotyczące typu zasobu, ważności, środowiska lub innych kryteriów, które są dla Ciebie ważne, użyj filtrów opcjonalnych powyżej listy zaleceń.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtry służące do uściślenia listy zaleceń Azure Security Center":::

1. Rozwiń formant i wybierz konkretne zalecenie, aby wyświetlić stronę szczegóły rekomendacji.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Strona szczegółów rekomendacji." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Strona zawiera następujące:

    1. W przypadku obsługiwanych zaleceń na górnym pasku narzędzi są wyświetlane dowolne lub wszystkie z następujących przycisków:
        - **Wymuszaj** i **Odmów** (zobacz [zapobieganie błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md))
        - **Wyświetl definicję zasad** , aby przejść bezpośrednio do wpisu Azure Policy dla podstawowych zasad
    1. **Wskaźnik ważności**
    1. **Interwał Aktualności** (jeśli dotyczy)
    1. **Liczba wykluczonych zasobów** w przypadku, gdy istnieją wykluczenia dla tego zalecenia, spowoduje to wyświetlenie liczby wykluczonych zasobów
    1. **Opis** — Krótki opis problemu
    1. **Kroki zaradcze** — opis ręcznych kroków wymaganych do skorygowania problemu z zabezpieczeniami odpowiednich zasobów. W przypadku rekomendacji z opcją "szybkie rozwiązanie" można wybrać opcję **Wyświetl logikę korygowania** przed zastosowaniem sugerowanej poprawki do zasobów. 
    1. **Zasoby, których to dotyczy** — zasoby są pogrupowane na karty:
        - **Zasoby w dobrej kondycji** — odpowiednie zasoby, na które nie ma wpływu lub na które rozwiązanie problemu zostało już skorygowane.
        - **Zasoby w złej kondycji** — zasoby, na które nadal mają wpływ zidentyfikowane problemy.
        - **Nie dotyczy zasobów** — zasoby, dla których zalecenie nie może udzielić ostatecznej odpowiedzi. Karta nie dotyczy również zawiera przyczyny dla każdego zasobu. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Nie dotyczy zasobów z przyczyn.":::
    1. Przyciski akcji do korygowania zalecenia lub wyzwalania aplikacji logiki.

## <a name="preview-recommendations"></a>Zalecenia dotyczące wersji zapoznawczej

Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku.

Powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, więc po zakończeniu okresu korzystania z wersji zapoznawczej będą one wchodzić w skład Twojego wyniku.

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Zalecenie z flagą wersji zapoznawczej":::
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w Security Center. Aby uzyskać informacje pokrewne:

- [Skoryguj zalecenia](security-center-remediate-recommendations.md)— Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zapobiegaj błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).
- [Automatyzowanie odpowiedzi na Security Center wyzwalacze](workflow-automation.md)— Automatyzowanie odpowiedzi na zalecenia
- [Wykluczanie zasobu z rekomendacji](exempt-resource.md)
- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](recommendations-reference.md)