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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 4d65b43dad80cb130d582132d21e2d10bd8051dc
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791388"
---
# <a name="security-recommendations-in-azure-security-center"></a>Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure 
W tym temacie wyjaśniono, jak wyświetlać i zrozumieć zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnikiem krok po kroku.
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?

Zalecenia to akcje, które należy wykonać, aby zabezpieczyć zasoby.

Security Center okresowe Analizowanie stanu zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich usuwania.

Każde zalecenie oferuje następujące informacje:

- Krótki opis tego, co jest zalecane.
- Kroki korygowania, które należy wykonać w celu wdrożenia zalecenia. <!-- In some cases, Quick Fix remediation is available. -->
- Które zasoby są wymagane do wykonania zalecanej akcji.
- Istotny **wpływ na ocenę**, czyli ilość, która będzie obowiązywać w przypadku zaimplementowania tego zalecenia.

## <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania<a name="monitor-recommendations"></a>

Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. Kafelek **zalecenia** w obszarze **Przegląd** przedstawia łączną liczbę zaleceń identyfikowanych przez Security Center.

![Omówienie usługi Security Center](./media/security-center-recommendations/asc-overview.png)

1. Wybierz **kafelek rekomendacje** w obszarze **Przegląd**. Zostanie otwarta lista **rekomendacje** .

      ![Wyświetlanie zaleceń](./media/security-center-recommendations/view-recommendations.png)

    Możesz filtrować zalecenia. Aby filtrować zalecenia, wybierz pozycję **Filtruj** w bloku **zalecenia** . Zostanie otwarty blok **filtru** i zostanie wybrana wartość ważność i stan, które mają być wyświetlane.

   * **Zalecenia**: zalecenie.
   * **Bezpieczny wpływ na ocenę**: wynik wygenerowany przez Security Center przy użyciu zaleceń dotyczących zabezpieczeń oraz stosowanie zaawansowanych algorytmów w celu określenia, jak kluczowe jest każde zalecenie. Aby uzyskać więcej informacji, zobacz temat [bezpieczne Obliczanie wyniku](secure-score-security-controls.md#how-your-secure-score-is-calculated).
   * **Zasób**: zawiera listę zasobów, do których stosuje się to zalecenie.
   * **Paski stanu**: opisuje ważność danego zalecenia:
       * **Wysoka (czerwony)**: istnieje luka w zabezpieczeniach, która ma zrozumiały zasób (na przykład aplikację, maszynę wirtualną lub sieciową grupę zabezpieczeń) i wymaga uwagi.
       * **Średni (pomarańczowy)**: istnieje luka w zabezpieczeniach, która nie jest krytyczna lub dodatkowe kroki są wymagane do eliminacji lub ukończenia procesu.
       * **Niska (niebieska)**: istnieje luka w zabezpieczeniach, którą należy rozwiązać, ale nie wymaga natychmiastowej uwagi. (Domyślnie małe rekomendacje nie są wyświetlane, ale można filtrować według niskich zaleceń, jeśli chcesz je zobaczyć). 
       * **Dobra kondycja (zielony)**:
       * **Niedostępne (szare)**:

1. Aby wyświetlić szczegóły każdego zalecenia, kliknij zalecenie.

    ![Szczegóły rekomendacji](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Zobacz [klasyczne i Menedżer zasobów modele wdrażania](../azure-classic-rm.md) dla zasobów platformy Azure.
 
## <a name="next-steps"></a>Następne kroki

W tym dokumencie wprowadzono zalecenia dotyczące zabezpieczeń w Security Center. Aby dowiedzieć się, jak skorygować zalecenia:

* [Skoryguj zalecenia](security-center-remediate-recommendations.md) — Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
