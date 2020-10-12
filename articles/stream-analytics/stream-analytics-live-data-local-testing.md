---
title: Przetestuj dane na żywo za pomocą Azure Stream Analytics dla programu Visual Studio
description: Dowiedz się, jak testować zadanie Azure Stream Analytics lokalnie przy użyciu danych przesyłanych strumieniowo na żywo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fe1caca11ec0a3d89ddae140d00cd3556b9d3d89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044468"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio (wersja zapoznawcza)

Narzędzia Azure Stream Analytics Tools for Visual Studio umożliwiają testowanie zadań lokalnie z poziomu środowiska IDE przy użyciu strumieni zdarzeń na żywo z usługi Azure Event Hub, IoT Hub i Blob Storage. Testowanie lokalne danych na żywo nie może zastąpić [testów wydajności i skalowalności](stream-analytics-streaming-unit-consumption.md) , które można wykonywać w chmurze, ale możesz zaoszczędzić czas podczas testowania funkcjonalnego, nie trzeba przesyłać do chmury za każdym razem, gdy chcesz przetestować zadanie Stream Analytics. Ta funkcja jest w wersji zapoznawczej i nie powinna być używana na potrzeby obciążeń produkcyjnych.

## <a name="testing-options"></a>Opcje testowania

Obsługiwane są następujące opcje testowania lokalnego:

|**Dane wejściowe**  |**Dane wyjściowe**  |**Typ zadania**  |
|---------|---------|---------|
|Lokalne dane statyczne   |  Lokalne dane statyczne   |   Chmura/krawędź |
|Dane wejściowe na żywo   |  Lokalne dane statyczne   |   Chmura |
|Dane wejściowe na żywo   |  Dane wyjściowe na żywo   |   Chmura |

## <a name="local-testing-with-live-data"></a>Testowanie lokalne z danymi dynamicznymi

1. Po utworzeniu [projektu w chmurze Azure Stream Analytics w programie Visual Studio](stream-analytics-quick-create-vs.md)Otwórz polecenie **Script. asaql**. Lokalne testowanie domyślnie używa lokalnych danych wejściowych i lokalnych.

   ![Azure Stream Analytics lokalne dane wejściowe i lokalne dla programu Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Aby przetestować dane na żywo, wybierz pozycję **Użyj danych wejściowych w chmurze** z pola listy rozwijanej.

   ![Azure Stream Analytics dane wejściowe w chmurze programu Visual Studio Live](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Ustaw **czas rozpoczęcia** , aby określić, kiedy zadanie rozpocznie przetwarzanie danych wejściowych. Aby zapewnić dokładne wyniki, zadanie może wymagać odczytania danych wejściowych przed czasem. Domyślny czas jest ustawiany na 30 minut przed bieżącą godziną.

   ![Azure Stream Analytics czas rozpoczęcia danych na żywo w programie Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kliknij pozycję **Uruchom lokalnie**. Zostanie wyświetlone okno konsoli z uruchomionymi postępami i metrykami zadań. Jeśli chcesz zatrzymać proces, możesz to zrobić ręcznie. 

   ![Okno Azure Stream Analytics procesu danych na żywo w programie Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Wyniki wyjściowe są odświeżane co trzy sekundy przy użyciu pierwszych 500 wierszy danych wyjściowych w oknie wyników lokalnego uruchomienia, a pliki wyjściowe są umieszczane w folderze **ASALocalRun** ścieżki projektu. Pliki wyjściowe można także otworzyć, klikając przycisk **Otwórz folder wyników** w oknie wynik uruchomienia lokalnego.

   ![Folder wyników otwartych danych programu Azure Stream Analytics Visual Studio Live](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Jeśli chcesz wyprowadzać wyniki do ujścia danych wyjściowych w chmurze, wybierz pozycję **dane wyjściowe do chmury** z drugiego pola listy rozwijanej. Power BI i Azure Data Lake Storage nie są obsługiwanymi ujściami wyjściowymi.

   ![Azure Stream Analytics dane wyjściowe danych na żywo w programie Visual Studio w chmurze](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Ograniczenia

* Power BI i Azure Data Lake Storage nie są obsługiwane przez ujścia danych wyjściowych ze względu na ograniczenia modelu uwierzytelniania.

* Tylko opcje danych wejściowych w chmurze mają obsługę [zasad czasu](stream-analytics-out-of-order-and-late-events.md) , podczas gdy lokalne Opcje wejściowe nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
