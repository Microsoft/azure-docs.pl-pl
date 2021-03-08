---
title: Szczegóły powierzchni niestandardowej w alertach wskaźnikowych platformy Azure | Microsoft Docs
description: Wyodrębnij i Dostosuj szczegóły zdarzeń niestandardowych w alertach w regułach analizy wskaźnikowej platformy Azure, aby uzyskać lepsze i pełniejsze informacje o zdarzeniach
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456258"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Szczegóły niestandardowego zdarzenia na liście alertów w usłudze Azure — wskaźnik 

> [!IMPORTANT]
>
> - Funkcja szczegóły niestandardowe jest dostępna w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="introduction"></a>Wprowadzenie

[Zaplanowane reguły analizy zapytań](tutorial-detect-threats-custom.md) analizują **zdarzenia** ze źródeł danych połączonych z platformą Azure, a następnie generują **alerty** , gdy zawartość tych zdarzeń jest istotna z perspektywy zabezpieczeń. Te alerty są dalej analizowane, pogrupowane i filtrowane według różnych aparatów (wskaźnik na platformie Azure) i są nadal uwzględniane w **zdarzeniach** , które wymagają uwagi analityka SOC. Jednak gdy analityk przegląda zdarzenie, tylko właściwości alertów składników są natychmiast widoczne. Wprowadzenie do rzeczywistej zawartości — informacje zawarte w zdarzeniach — wymagają wykonania niektórych przeszukiwanie stosów.

Korzystając z funkcji **szczegóły niestandardowe** w **Kreatorze reguł analizy**, można wyświetlać dane zdarzeń w alertach, które są tworzone na podstawie tych zdarzeń, co sprawia, że dane zdarzenia są częścią właściwości alertu. W efekcie daje to natychmiastowe wgląd w zawartość zdarzeń w zdarzeniach, co pozwala na klasyfikacja, badanie, rysowanie wniosków i reagowanie na znacznie większą szybkość i wydajność.

Opisana poniżej procedura jest częścią Kreatora tworzenia reguł analizy. Jest on traktowany w tym miejscu niezależnie w celu rozwiązania scenariusza dodawania lub zmieniania niestandardowych szczegółów w istniejącej regule analizy.

## <a name="how-to-surface-custom-event-details"></a>Jak wyświetlić szczegóły zdarzenia niestandardowego

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Analiza**.

1. Wybierz zaplanowaną regułę zapytania, a następnie kliknij przycisk **Edytuj**. Lub Utwórz nową regułę, klikając pozycję **utwórz &#10132; zaplanowaną regułę zapytania** w górnej części ekranu.

1. Kliknij kartę **Ustawianie logiki reguły** .

1. W sekcji **ulepszenie alertów** wybierz pozycję **szczegóły niestandardowe**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Znajdź i wybierz Szczegóły niestandardowe":::

1. W sekcji teraz rozwinięte **szczegóły niestandardowe** Dodaj pary klucz-wartość odpowiadające szczegółom, które chcesz przedstawić:

    1. W polu **klucz** wprowadź wybraną nazwę, która będzie wyświetlana jako nazwa pola w obszarze alerty.

    1. W polu **wartość** wybierz parametr zdarzenia, który ma być wyświetlane na liście alertów z listy rozwijanej. Ta lista zostanie wypełniona przez wartości odpowiadające polom w tabelach, które są podmiotem zapytania reguły.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Dodaj szczegóły niestandardowe":::

1. Kliknij przycisk **Dodaj nowy** , aby uzyskać więcej szczegółów, powtarzając ostatnią procedurę definiowania par klucz-wartość. 

    Jeśli zmienisz zdanie lub pomyłkę, możesz usunąć szczegóły niestandardowe, klikając ikonę kosza obok listy rozwijanej **wartość** dla tych szczegółów.

1. Po zakończeniu definiowania szczegółów niestandardowych kliknij kartę **Przegląd i tworzenie** . Po pomyślnym sprawdzeniu reguły kliknij przycisk **Zapisz**.

    > [!NOTE]
    > 
    >**Limity usługi**
    > - Można zdefiniować **maksymalnie 20 szczegółów niestandardowych** w ramach jednej reguły analizy.
    >
    > - Limit rozmiaru wszystkich szczegółów niestandardowych, łącznie, wynosi **2 KB**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób przedstawiania niestandardowych szczegółów w alertach przy użyciu reguł analizy wskaźnikowej platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Pobierz kompletny obraz dla [zaplanowanych reguł analizy zapytań](tutorial-detect-threats-custom.md).
- Dowiedz się więcej o [jednostkach na platformie Azure](entities-in-azure-sentinel.md).
