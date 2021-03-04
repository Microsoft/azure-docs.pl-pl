---
title: Koryguj zalecenia w Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak odpowiedzieć na zalecenia w Azure Security Center, aby chronić zasoby i spełnić zasady zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 6222491d35317e549a3d21f29395b9c25813ea9f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100359"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Rekomendacje dotyczące korygowania w usłudze Azure Security Center

Zalecenia zawierają sugestie dotyczące lepszego zabezpieczania zasobów. Zalecenie jest implementowane przez wykonanie czynności zaradczych, które zostały podane w zaleceniu.

## <a name="remediation-steps"></a>Kroki zaradcze <a name="remediation-steps"></a>

Po przejrzeniu wszystkich zaleceń Zdecyduj, które z nich należy skorygować w pierwszej kolejności. Zalecamy, aby określić priorytety kontroli zabezpieczeń z największą możliwością zwiększenia bezpiecznego wyniku.

1. Z listy wybierz zalecenie.

1. Postępuj zgodnie z instrukcjami w sekcji **kroki zaradcze** . Każde zalecenie ma swój własny zestaw instrukcji. Poniższy zrzut ekranu przedstawia kroki korygowania dotyczące konfigurowania aplikacji tak, aby zezwalały na ruch za pośrednictwem protokołu HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Kroki ręcznego korygowania zalecenia" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Po zakończeniu zostanie wyświetlone powiadomienie informujące o tym, czy problem został rozwiązany.

## <a name="quick-fix-remediation"></a>Szybkie rozwiązywanie problemów

Aby uprościć korygowanie i poprawić bezpieczeństwo środowiska (i zwiększyć swój Bezpieczny wynik), wiele zaleceń obejmuje opcję szybkiej naprawy.

Szybka poprawka pomaga szybko skorygować zalecenia dotyczące wielu zasobów.

> [!TIP]
> Rozwiązania szybkiej naprawy są dostępne tylko dla konkretnych zaleceń. Aby znaleźć zalecenia, które mają dostępną szybką poprawkę, Użyj filtru **Akcje odpowiedzi** dla listy zaleceń:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Użyj filtrów powyżej listy zaleceń, aby znaleźć zalecenia z opcją szybkie rozwiązanie":::

Aby zaimplementować rozwiązanie szybkiej naprawy:

1. Z listy zaleceń, które mają **szybką poprawkę!** Wybierz zalecenie.

    [![Wybierz pozycję szybka naprawa.](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Na karcie **zasoby w złej kondycji** Wybierz zasoby, dla których chcesz wdrożyć zalecenie, a następnie wybierz pozycję **Koryguj**.

    > [!NOTE]
    > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.

1. W polu potwierdzenia Przeczytaj szczegóły dotyczące korygowania i implikacje.

    ![Szybka naprawa](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Implikacje są wymienione w szarym polu okna **korygowanie zasobów** , które jest otwierane po kliknięciu przycisku **Koryguj**. Wyświetlają one zmiany, które są wykonywane podczas korygowania szybkiej korekty.

1. W razie potrzeby Wstaw odpowiednie parametry i zatwierdź korygowanie.

    > [!NOTE]
    > Ukończenie korygowania zasobów na karcie **zasoby w dobrej kondycji** może potrwać kilka minut. Aby wyświetlić akcje korygowania, sprawdź [Dziennik aktywności](#activity-log).

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szybkie rozwiązywanie problemów z rejestrowaniem w dzienniku aktywności <a name="activity-log"></a>

Operacja korygowania używa wdrożenia szablonu lub wywołania interfejsu API poprawek REST w celu zastosowania konfiguracji w zasobie. Te operacje są rejestrowane w [dzienniku aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korygowania zaleceń w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące strony:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure
* [Co to są zasady zabezpieczeń, inicjatywy i zalecenia?](security-policy-concept.md)