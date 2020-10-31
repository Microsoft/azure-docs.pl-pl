---
title: Monitoruj Azure Stream Analytics i zarządzaj nimi za pomocą programu Visual Studio
description: W tym artykule opisano, jak za pomocą programu Visual Studio monitorować zadania Azure Stream Analytics i zarządzać nimi.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a151d4ce5a2f9860c3b7919496a2edd25cc58ede
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123629"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitoruj Stream Analytics zadania i zarządzaj nimi za pomocą programu Visual Studio

W tym artykule przedstawiono sposób monitorowania zadania Stream Analytics w programie Visual Studio. Narzędzia Azure Stream Analytics Tools for Visual Studio zapewniają środowisko monitorowania podobne do Azure Portal bez konieczności opuszczania środowiska IDE. Możesz rozpocząć monitorowanie zadania zaraz po **przesłaniu na platformę Azure** ze **skryptu. asaql** lub monitorować istniejące zadania, niezależnie od sposobu ich tworzenia. 

## <a name="job-summary"></a>Podsumowanie zadania

**Podsumowanie zadania** i **metryki zadań** umożliwiają szybką migawkę zadania. W skrócie możesz określić stan zadania i informacje o zdarzeniu.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Metryki zadań

Możesz zwinąć **Podsumowanie zadania** i kliknąć kartę **metryki zadania** , aby wyświetlić wykres z ważnymi metrykami. Sprawdź i usuń zaznaczenie typów metryk, aby dodawać i usuwać je z grafu.

![Metryki Stream Analytics w programie Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitorowanie błędów

Błędy można także monitorować, klikając kartę **Błędy** .

![Błędy Stream Analytics w programie Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Tworzenie zadania Azure Stream Analytics za pomocą programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Zainstaluj narzędzia Azure Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md)