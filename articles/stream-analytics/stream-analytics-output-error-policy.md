---
title: Wyjściowe zasady błędów w Azure Stream Analytics
description: Dowiedz się więcej o zasadach obsługi błędów wyjściowych dostępnych w Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 923e8d1ffc3e606c16226b4b2ccbdcfaf4d3e9f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123425"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics wyjściowe zasady błędów
W tym artykule opisano zasady obsługi błędów danych wyjściowych, które można skonfigurować w Azure Stream Analytics.

Zasady obsługi błędów danych wyjściowych mają zastosowanie tylko do błędów konwersji danych, które występują, gdy zdarzenie wyjściowe generowane przez zadanie Stream Analytics jest niezgodne ze schematem docelowego ujścia. Te zasady można skonfigurować, wybierając pozycję **Ponów** lub **upuść** . W Azure Portal, w ramach zadania Stream Analytics, w obszarze **Konfiguruj** wybierz pozycję **zasady błędów** , aby dokonać wyboru.

![Lokalizacja zasad błędów danych wyjściowych Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Ponów próbę
Gdy wystąpi błąd, Azure Stream Analytics ponawianie próby zapisania zdarzenia przez czas, dopóki zapis nie powiedzie się. Nie ma limitu czasu dla ponownych prób. Ostatecznie wszystkie kolejne zdarzenia są blokowane przed przetwarzaniem przez zdarzenie, które jest ponawiane. Ta opcja jest domyślną zasadą obsługi błędów wyjścia.

## <a name="drop"></a>Listy rozwijanej
Usługa Azure Stream Analytics porzuci wszelkie zdarzenia wyjściowe powodujące błędy konwersji danych. Porzuconych zdarzeń nie można odzyskać na potrzeby ponownego przetwarzania później.


Wszystkie błędy przejściowe (na przykład błędy sieciowe) są ponawiane niezależnie od konfiguracji wyjściowych zasad obsługi błędów.


## <a name="next-steps"></a>Następne kroki
[Przewodnik rozwiązywania problemów Azure Stream Analytics](./stream-analytics-troubleshoot-query.md)