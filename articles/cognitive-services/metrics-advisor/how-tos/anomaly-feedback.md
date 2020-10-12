---
title: Zapewnianie nietypowej opinii dotyczącej usługi Advisor metryk
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wysłać opinię na temat anomalii znalezionych przez wystąpienie usługi Advisor metryk i dostosować wyniki.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938047"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Dostosowywanie wykrywania anomalii przy użyciu opinii

Jeśli niektóre wyniki wykrywania anomalii dostarczone przez Monitor metryk nie są zadowalające, można ręcznie dodać opinie, które wpłyną na model zastosowany do danych. 

Użyj przycisków w prawym górnym rogu strony, aby uaktywnić tryb adnotacji z opinią.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Tryb adnotacji z opinią.":::

Po aktywowaniu trybu adnotacji z opinią można przesłać opinię dotyczącą jednego punktu lub wielu punktów ciągłych.

## <a name="give-feedback-for-one-point"></a>Przekaż opinię dla jednego punktu 

Gdy tryb adnotacji z opinią został aktywowany, kliknij punkt, aby otworzyć panel **Dodaj opinię** . Możesz ustawić typ informacji zwrotnych, które chcesz zastosować. Ta opinia zostanie zarejestrowana w celu wykrycia w przyszłości.  

* Wybierz pozycję **anomalia** , jeśli uważasz, że punkt został niepoprawnie oznaczony jako monitor metryk. Można określić, czy punkt powinien czy nie powinien być anomalią. 
* Wybierz pozycję **ChangePoint** , jeśli uważasz, że punkt wskazuje początek zmiany trendu.
* Wybierz **okres** , aby wskazać sezonowości. Monitor metryki może automatycznie wykrywać interwały sezonowości. można też określić to ręcznie. 

Rozważ pozostawienie komentarza w polu tekstowym **komentarza** w tym samym czasie, a następnie kliknij przycisk **Zapisz** , aby zapisać swoją opinię.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Tryb adnotacji z opinią.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Prześlij opinię na temat wielu punktów ciągłych

Możesz przesłać opinię dla wielu punktów ciągłych jednocześnie, klikając przycisk w dół i przeciągając myszą na punkty, do których chcesz dodać adnotację. Zobaczysz to samo menu opinii jak powyżej. Ta sama opinia zostanie zastosowana do wszystkich wybranych punktów po kliknięciu przycisku **Zapisz**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Tryb adnotacji z opinią.":::

## <a name="how-to-view-my-feedback"></a>Jak wyświetlić opinię

Aby sprawdzić, czy wykrycie anomalii punktu zostało zmienione, umieść kursor nad punktem. Na etykietce narzędzia zostanie wyświetlona **informacja zwrotna: prawda** , jeśli wykrycie zostało zmienione. Jeśli zostanie wyświetlona **wartość false**, na tym etapie zostanie wykonane Obliczanie opinii, ale wynik wykrywania anomalii nie został zmieniony.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Tryb adnotacji z opinią.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Kiedy należy dodać adnotację do anomalii jako "normalne"

Istnieje wiele powodów, dla których może być rozważana nietypowe alarmy. Należy rozważyć użycie następujących funkcji klasyfikatora metryk, jeśli są spełnione następujące scenariusze:


|Scenariusz  |Zalecenie |
|---------|---------|
|Anomalia jest spowodowana przez znaną zmianę źródła danych, na przykład zmianę systemu.     | Nie należy dodawać adnotacji do tej anomalii, jeśli ten scenariusz nie powinien być regularnie powtarzany.        |
|Anomalia jest spowodowana przez dni wolne od pracy.     | Użyj [wstępnie ustawionych zdarzeń](configure-metrics.md#preset-events) do oflagowania wykrywania anomalii w określonym czasie.       |
|Istnieje regularny wzorzec wykrytych anomalii (na przykład weekendy) i nie powinny one być anomaliami.      |Użyj funkcji opinii lub wstępnie ustawionych zdarzeń.        |

## <a name="next-steps"></a>Następne kroki
- [Diagnozuj zdarzenie](diagnose-incident.md).
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
- [Konfigurowanie alertów i otrzymywanie powiadomień przy użyciu wpięcia](../how-tos/alerts.md)