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
ms.openlocfilehash: 7f6c0f2a311590219fb59bfe1ec63831c03e8af2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314440"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlać i zrozumieć zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnikiem krok po kroku.
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?

Zalecenia to akcje, które należy wykonać, aby zabezpieczyć zasoby.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Każde zalecenie oferuje następujące informacje:

- Krótki opis problemu.
- Kroki korygowania, które należy wykonać w celu wdrożenia zalecenia.
- Zasoby, których to dotyczy.

## <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania <a name="monitor-recommendations"></a>

Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. Kafelek **zalecenia** w obszarze **Przegląd** przedstawia łączną liczbę zaleceń identyfikowanych przez Security Center.

![Omówienie usługi Security Center](./media/security-center-recommendations/asc-overview.png)

1. Wybierz **kafelek rekomendacje** w obszarze **Przegląd**. Zostanie otwarta lista **rekomendacje** .

1. Zalecenia są pogrupowane w zabezpieczeniach.

      ![Zalecenia pogrupowane według kontroli zabezpieczeń](./media/security-center-recommendations/view-recommendations.png)

1. Rozwiń formant i wybierz określone zalecenie, aby wyświetlić stronę rekomendacja.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Strona szczegółów rekomendacji." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Strona zawiera następujące:

    - Przyciski **Wymuś** i **Odmów** w przypadku obsługiwanych zaleceń (zobacz zapobieganie błędom [konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md))
    - **Wskaźnik ważności**
    - **Interwał Aktualności**  (jeśli dotyczy) 
    - **Opis** — Krótki opis problemu
    - **Kroki zaradcze** — opis ręcznych kroków wymaganych do skorygowania problemu z zabezpieczeniami odpowiednich zasobów. W przypadku rekomendacji z opcją "szybkie rozwiązanie" można wybrać opcję **Wyświetl logikę korygowania** przed zastosowaniem sugerowanej poprawki do zasobów. 
    - **Zasoby, których to dotyczy** — zasoby są pogrupowane na karty:
        - **Zasoby w dobrej kondycji** — odpowiednie zasoby, na które nie ma wpływu lub na które rozwiązanie problemu zostało już skorygowane.
        - **Zasoby w złej kondycji** — zasoby, na które nadal mają wpływ zidentyfikowane problemy.
        - **Nie dotyczy zasobów** — zasoby, dla których zalecenie nie może udzielić ostatecznej odpowiedzi. Karta nie dotyczy również zawiera przyczyny dla każdego zasobu. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Nie dotyczy zasobów z przyczyn.":::

## <a name="preview-recommendations"></a>Zalecenia dotyczące wersji zapoznawczej

Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku.

Powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, więc po zakończeniu okresu korzystania z wersji zapoznawczej będą one wchodzić w skład Twojego wyniku.

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Zalecenie z flagą wersji zapoznawczej":::
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w Security Center. Aby dowiedzieć się, jak skorygować zalecenia:

- [Skoryguj zalecenia](security-center-remediate-recommendations.md) — Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zapobiegaj błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).
