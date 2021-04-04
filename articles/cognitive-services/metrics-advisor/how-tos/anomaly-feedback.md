---
title: Zapewnianie nietypowej opinii dotyczącej usługi Advisor metryk
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wysłać opinię na temat anomalii znalezionych przez wystąpienie usługi Advisor metryk i dostosować wyniki.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184973"
---
# <a name="provide-anomaly-feedback"></a>Zapewnianie nietypowej opinii

Opinie użytkowników to jedna z najważniejszych metod wykrywania usterek w systemie wykrywania anomalii. W tym miejscu możemy umożliwić użytkownikom oznaczenie nieprawidłowych wyników wykrywania bezpośrednio w szeregach czasowych i natychmiastowe zastosowanie informacji zwrotnych. W ten sposób użytkownik może nauczyć się systemu wykrywania anomalii, jak przeprowadzić wykrywanie anomalii dla określonej szeregu czasowego poprzez aktywne interakcje. 

> [!NOTE]
> Obecnie opinia ma wpływ tylko na wyniki wykrywania anomalii przez **Inteligentne wykrywanie** , ale nie **próg twardy** i **próg zmian**.

## <a name="how-to-give-time-series-feedback"></a>Jak zapewnić opinię dla szeregów czasowych

Możesz przekazać opinię na stronie szczegółów metryk w dowolnej serii. Po prostu zaznacz dowolny punkt, a zobaczysz poniższe okno dialogowe opinii. Pokazuje on wymiary wybranej serii. Możesz wybrać pozycję wartości wymiarów lub nawet usunąć niektóre z nich, aby uzyskać wsadowe dane szeregów czasowych. Po wybraniu szeregów czasowych wybierz przycisk **Dodaj** , aby dodać swoją opinię. można podać cztery rodzaje opinii. Aby dołączyć wiele elementów opinii, wybierz przycisk **Zapisz** po zakończeniu adnotacji.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Wykresy z danymi szeregów czasowych z niebieską linią i czerwonymi kropkami w różnych punktach. Czerwone pole otaczające jeden punkt z tekstem: zaznacz dowolny punkt":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Okno dialogowe Dodawanie opinii z dwoma wymiarami oraz opcjami wyboru lub usuwania wymiarów oraz dodawania opinii.":::

### <a name="mark-the-anomaly-point-type"></a>Oznacz typ punktu anomalii

Jak pokazano na poniższej ilustracji, w oknie dialogowym opinii będzie automatycznie wypełniana sygnatura czasowa wybranego punktu, chociaż można edytować tę wartość. Następnie wybierz, czy chcesz zidentyfikować ten element jako `Anomaly` , `NotAnomaly` lub `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Menu rozwijane z opcjami anomalii, NotAnomaly i autowykrywania":::

Wybór będzie stosował swoją opinię do przyszłego przetwarzania wykrywania anomalii w tej samej serii. Przetworzone punkty nie zostaną ponownie obliczone. Oznacza to, że w przypadku wybrania anomalii jako NotAnomaly, firma Microsoft będzie pomijać podobne anomalie w przyszłości, a jeśli użytkownik oznaczył `NotAnomaly` punkt jako `Anomaly` , będzie mógł wykryć podobne punkty jak `Anomaly` w przyszłości. `AutoDetect`W przypadku wybrania tej opcji każda Poprzednia opinia w tym samym punkcie będzie ignorowana w przyszłości.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Prześlij opinię na temat wielu punktów ciągłych 

Jeśli chcesz, aby w tym samym czasie przekazać nietypowe Opinie dla wielu punktów ciągłych, wybierz grupę punktów, do których chcesz dodać adnotację. Wybrany zakres czasu zostanie wyświetlony automatycznie po podaniu nietypowej opinii.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Menu opinii o anomalii z wybraną anomalią i określonym zakresem czasu":::

Aby sprawdzić, czy w poszczególnych punktach ma wpływ opinia o anomalii, podczas przeglądania szeregów czasowych wybierz jeden punkt. Jeśli wynik wykrywania anomalii został zmieniony przez opinię, na etykietce narzędzia zostanie wyświetlona **informacja zwrotna: prawda**. Jeśli zostanie ona wyświetlona na **podstawie opinii: FAŁSZ**, oznacza to, że dla tego punktu przeprowadzono obliczanie informacji o anomalii, ale wynik wykrywania anomalii nie powinien zostać zmieniony.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Etykietka narzędzia wyświetlana z wyrazami: dotyczy opinii: prawda, wyróżnione czerwonymi":::

Istnieją sytuacje, w których nie sugerujemy przekazywania opinii:

- Anomalia jest powodowana przez dni wolne od pracy. Zalecane jest używanie wstępnie zdefiniowanego zdarzenia do rozwiązywania tego rodzaju fałszywych alarmów, ponieważ będzie bardziej precyzyjna.
- Anomalia jest spowodowana przez znaną zmianę źródła danych. Na przykład w tym czasie nastąpiła zmiana systemu nadrzędnego. W takiej sytuacji oczekuje się, że alert anomalii, ponieważ system nie wie, co spowodowało zmianę wartości i gdy podobne zmiany wartości zostaną wykonane ponownie. Dlatego nie sugerujemy dodawania adnotacji tego rodzaju problemu jako `NotAnomaly` .

## <a name="change-points"></a>Punkty zmian

Czasami zmiana trendu danych wpłynie na wyniki wykrywania anomalii. W przypadku podjęcia decyzji o tym, czy punkt jest anomalią, czy nie, będzie brane pod uwagę najnowsze okno danych historii. Gdy Twoja seria czasowa ma zmianę trendu, można oznaczyć dokładny punkt zmiany, co pomoże naszym detektorowi anomalii w przyszłości.

Jak widać na poniższej ilustracji, możesz wybrać `ChangePoint` Typ opinii i wybrać opcję `ChangePoint` , `NotChangePoint` lub `AutoDetect` z listy rozwijanej.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menu Zmień punkt z listą rozwijaną zawierającą opcje ChangePoint, NotChangePoint i autowykrywania":::

> [!NOTE]
> Jeśli Twoje dane nie zmieniają się, wystarczy oznaczyć tylko jeden punkt jako `ChangePoint` , więc jeśli oznaczyłsz `timerange` , zostanie wypełniona sygnatura czasowa ostatniego punktu. W takim przypadku adnotacja będzie miała wpływ tylko na wyniki wykrywania anomalii po 12 punktach.

## <a name="seasonality"></a>Sezonowość

W przypadku danych sezonowych podczas przeprowadzania wykrywania anomalii jednym krokiem jest oszacowanie okresu (sezonowości) szeregów czasowych i zastosowanie go do fazy wykrywania anomalii. Czasami trudno jest zidentyfikować dokładny okres i okres może ulec zmianie. Nieprawidłowo zdefiniowany okres może mieć wpływ na wyniki wykrywania anomalii. Bieżący okres można znaleźć z poziomu etykietki narzędzia, jej nazwa to `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Etykieta narzędzia jest nakładana na kropki i liczbę siedmiu wyróżnionych na czerwono.":::

Możesz przekazać opinię dotyczącą okresu, aby naprawić ten rodzaj błędu wykrywania anomalii. Jak widać na ilustracji, można ustawić wartość okresu. Jednostka `interval` oznacza jedną stopień szczegółowości. W tym przypadku zero interwałów oznacza, że dane nie są sezonowe. Możesz również wybrać, `AutoDetect` czy chcesz anulować poprzednią opinię, i powiadom o automatycznym wykryciu okresu. 
 
> [!NOTE]
> W przypadku ustawienia okresu nie trzeba przypisywać sygnatury czasowej ani timerange, ten okres będzie miał wpływ na przyszłe wykrycia anomalii w całej szeregów czasowych od momentu przekazania opinii.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menu z okresem autowykrywania ma wartość 28, a interwał ustawiony na zero wskazujący, że nie jest sezonowy.":::

## <a name="provide-comment-feedback"></a>Podaj opinię w komentarzu

Możesz również dodawać komentarze do adnotacji i zapewnić kontekst dla danych. Aby dodać komentarze, wybierz zakres czasu i Dodaj tekst komentarza.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Z możliwością ustawienia zakresu czasu i pola w celu dodania komentarza tekstowego":::

## <a name="time-series-batch-feedback"></a>Opinia wsadowa szeregów czasowych

Jak opisano wcześniej, modalne informacje zwrotne umożliwiają ponownie wybór lub usunięcie wartości wymiarów, aby uzyskać partię szeregów czasowych zdefiniowanych przez filtr wymiaru. Możesz również otworzyć to modalne, klikając przycisk "+", aby uzyskać informacje zwrotne z lewego panelu, a następnie wybrać wymiary i wartości wymiarów.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menu z niebieskim znakiem plus wyróżniony czerwono obok wyrazu opinii":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Menu Dodaj opinię z dwoma wymiarami wskazywanymi przez Dim1 i Dim2":::

## <a name="how-to-view-feedback-history"></a>Jak wyświetlić historię opinii

Istnieją dwa sposoby wyświetlania historii opinii. Możesz wybrać przycisk Historia opinii z lewego panelu, aby zobaczyć modalną listę opinii. Zawiera listę wszystkich informacji zwrotnych, które zostały podane przed dla jednej serii lub filtrów wymiarów.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menu listy opinii &quot;":::

Innym sposobem wyświetlania historii opinii jest z serii. Zobaczysz kilka przycisków w prawym górnym rogu każdej serii. Wybierz przycisk Pokaż opinię, a wiersz będzie przełączać się między wyświetlaniem punktów anomalii w celu pokazywania wpisów opinii. Zielona Flaga reprezentuje punkt zmiany, a niebieskie punkty są innymi punktami opinii. Można je również wybrać i uzyskać modalną listę opinii, która zawiera szczegółowe informacje dotyczące opinii podanej w tym punkcie.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Wykres historii opinii":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menu listy opinii z dwoma wymiarami":::

> [!NOTE]
> Każdy, kto ma dostęp do tej metryki, może przekazać opinię, aby można było zobaczyć opinię podaną przez innych właścicieli źródła danych. Jeśli edytujesz ten sam punkt co ktoś inny, Twoje opinie zastąpią Poprzedni wpis opinii.       

## <a name="next-steps"></a>Następne kroki
- [Diagnozuj zdarzenie](diagnose-incident.md).
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
- [Konfigurowanie alertów i otrzymywanie powiadomień przy użyciu wpięcia](../how-tos/alerts.md)