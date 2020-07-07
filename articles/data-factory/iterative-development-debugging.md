---
title: Iteracyjne programowanie i debugowanie w Azure Data Factory
description: Informacje o sposobach tworzenia i debugowania potoków Data Factory w Azure Portal.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81411526"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Debugowanie i programowanie przyrostowe za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory umożliwia iteracyjne opracowywanie i debugowanie potoków Data Factory.

Aby uzyskać 8-minutowy wprowadzenie i demonstrację tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funkcje debugowania iteracyjnego
Tworzenie potoków i uruchamianie testów przy użyciu możliwości **debugowania** na kanwie potoku bez pisania pojedynczego wiersza kodu.

![Możliwość debugowania na kanwie potoku](media/iterative-development-debugging/iterative-development-image1.png)

Wyniki przebiegów testów są wyświetlane w oknie **dane wyjściowe** kanwy potoku.

![Okno dane wyjściowe kanwy potoku](media/iterative-development-debugging/iterative-development-image2.png)

Po pomyślnym uruchomieniu testu Dodaj więcej działań do potoku i Kontynuuj debugowanie w sposób iteracyjny. Możesz również **anulować** przebieg testu, gdy jest w toku.

![Anulowanie przebiegu testowego](media/iterative-development-debugging/iterative-development-image3.png)

Po uruchomieniu testów nie trzeba publikować zmian w fabryce danych przed wybraniem opcji **Debuguj**. Ta funkcja jest przydatna w scenariuszach, w których należy upewnić się, że zmiany działają zgodnie z oczekiwaniami przed aktualizacją przepływu pracy fabryki danych.

> [!IMPORTANT]
> Wybranie pozycji **Debuguj** powoduje rzeczywiste uruchomienie potoku. Tak więc, na przykład, jeśli potok zawiera działanie kopiowania, przebieg testu kopiuje dane ze źródła do miejsca docelowego. W związku z tym zalecamy korzystanie z folderów testowych w działaniach kopiowania i innych działaniach podczas debugowania. Po debugowaniu potoku przejdź do folderów rzeczywistych, które mają być używane w normalnych operacjach.

## <a name="visualizing-debug-runs"></a>Wizualizacja przebiegów debugowania

Można wizualizować wszystkie przebiegi debugowania, które są w toku dla fabryki danych w jednym miejscu. Wybierz pozycję **Wyświetl debugowanie przebiega** w prawym górnym rogu strony. Ta funkcja jest przydatna w scenariuszach, w których potoki główne uruchamiają uruchomienia debugowania dla potoków podrzędnych, i chcesz, aby jeden widok widział wszystkie aktywne uruchomienia debugowania.

![Wybierz ikonę Wyświetl aktywne uruchomienia debugowania](media/iterative-development-debugging/view-debug-runs-image1.png)

![Przykładowa lista aktywnych przebiegów debugowania](media/iterative-development-debugging/view-debug-runs-image2.png)

Jeśli masz aktywne sesje debugowania przepływu danych, te sesje będą wyświetlane w dolnej części aktywnego okna debugowania. Możesz wybrać aktywną sesję przepływu danych i zatrzymać odpowiedni klaster.

![Przykładowa lista aktywnych przebiegów debugowania przepływu danych](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorowanie przebiegów debugowania

Przebiegi testowe zainicjowane z możliwością **debugowania** nie są dostępne na liście na karcie **monitor** . Tylko uruchomienia wyzwalane przez wyzwalacze okna **Wyzwalanie teraz**, **harmonogram**lub **wirowania** można zobaczyć na karcie **monitorowanie** . Można zobaczyć ostatni przebieg testu zainicjowany z możliwością **debugowania** w oknie **danych wyjściowych** kanwy potoku.

## <a name="setting-breakpoints-for-debugging"></a>Ustawianie punktów przerwania na potrzeby debugowania

Data Factory umożliwia również debugowanie do momentu osiągnięcia określonego działania na kanwie potoku. Po prostu umieść punkt przerwania dla działania, do którego chcesz przeprowadzić test, a następnie wybierz pozycję **Debuguj**. Data Factory zapewnia, że test będzie wykonywany tylko do działania punktu przerwania na kanwie potoku. Ten *Debuguj do momentu* , gdy nie chcesz testować całego potoku, ale tylko podzbiór działań w potoku.

![Punkty przerwania na kanwie potoku](media/iterative-development-debugging/iterative-development-image4.png)

Aby ustawić punkt przerwania, wybierz element na kanwie potoku. Opcja *Debuguj do momentu* pojawi się jako puste czerwone koło w prawym górnym rogu elementu.

![Przed ustawieniem punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-image5.png)

Po wybraniu opcji *Debuguj do momentu* zostanie ona zmieniona na wypełniony czerwony okrąg, aby wskazać, że punkt przerwania jest włączony.

![Po ustawieniu punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Następne kroki
[Ciągła integracja i wdrażanie w Azure Data Factory](continuous-integration-deployment.md)
