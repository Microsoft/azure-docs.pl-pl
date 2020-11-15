---
title: Koryguj zalecenia w Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak skorygować zalecenia w Azure Security Center, aby chronić zasoby i zachować zgodność z zasadami zabezpieczeń.
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
ms.openlocfilehash: 3d4b968d0d5e091d72cf718d58b11ade68ddf8c6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637092"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Rekomendacje dotyczące korygowania w usłudze Azure Security Center

Zalecenia zawierają sugestie dotyczące lepszego zabezpieczania zasobów. Zalecenie jest implementowane przez wykonanie czynności zaradczych, które zostały podane w zaleceniu.

## <a name="remediation-steps"></a>Kroki zaradcze <a name="remediation-steps"></a>

Po przejrzeniu wszystkich zaleceń Zdecyduj, które z nich należy skorygować w pierwszej kolejności. Zalecamy użycie [wpływu na ocenę](security-center-recommendations.md#monitor-recommendations) , aby pomóc w ustaleniu, co należy zrobić w pierwszej kolejności.

1. Na liście kliknij zalecenie.

1. Postępuj zgodnie z instrukcjami w sekcji **kroki zaradcze** . Każde zalecenie ma swój własny zestaw instrukcji. Poniższy zrzut ekranu przedstawia kroki korygowania dotyczące konfigurowania aplikacji tak, aby zezwalały na ruch za pośrednictwem protokołu HTTPS.

    ![Szczegóły rekomendacji](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="quick-fix-remediation"></a>Szybkie rozwiązywanie problemów<a name="one-click"></a>

Szybka naprawa upraszcza korygowanie i pozwala szybko zwiększyć swój Bezpieczny wynik, zwiększając bezpieczeństwo środowiska.

Szybkie rozwiązanie pozwala szybko skorygować zalecenia dotyczące wielu zasobów. 

> [!TIP]
> Szybka poprawka jest dostępna tylko dla konkretnych zaleceń. Aby znaleźć zalecenia z opcją szybkie rozwiązanie, użyj dedykowanego filtru w górnej części listy zaleceń:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Użyj filtrów powyżej listy zaleceń, aby znaleźć zalecenia z opcją szybkie rozwiązanie":::

Aby zaimplementować szybkie korygowanie poprawek:

1. Z listy zaleceń, które mają **szybką poprawkę!** Kliknij zalecenie.

    [![Wybierz pozycję szybka naprawa.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na karcie **zasoby w złej kondycji** Wybierz zasoby, dla których chcesz wdrożyć zalecenie, a następnie kliknij przycisk **Koryguj**.

    > [!NOTE]
    > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.

1. W polu potwierdzenia Przeczytaj szczegóły dotyczące korygowania i implikacje.

    ![Szybka naprawa](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Implikacje są wymienione w szarym polu okna **korygowanie zasobów** , które jest otwierane po kliknięciu przycisku **Koryguj**. Wyświetlają one zmiany, które są wykonywane podczas korygowania szybkiej korekty.

1. W razie potrzeby Wstaw odpowiednie parametry i zatwierdź korygowanie.

    > [!NOTE]
    > Ukończenie korygowania zasobów na karcie **zasoby w dobrej kondycji** może potrwać kilka minut. Aby wyświetlić akcje korygowania, sprawdź [Dziennik aktywności](#activity-log).

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szybkie rozwiązywanie problemów z rejestrowaniem w dzienniku aktywności <a name="activity-log"></a>

Operacja korygowania używa wdrożenia szablonu lub wywołania interfejsu API poprawek REST w celu zastosowania konfiguracji w zasobie. Te operacje są rejestrowane w [dzienniku aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korygowania zaleceń w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
