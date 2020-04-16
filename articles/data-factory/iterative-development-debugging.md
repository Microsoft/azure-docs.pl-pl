---
title: Iteracyjny programowanie i debugowanie w usłudze Azure Data Factory
description: Dowiedz się, jak tworzyć i debugować potoki usługi Data Factory iteratively w witrynie Azure portal.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411526"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Debugowanie i programowanie przyrostowe za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usługa Azure Data Factory umożliwia iteracyjne tworzenie i debugowanie potoków fabryki danych.

Aby zapoznać się z ośmiominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funkcje debugowania iteracyjnego
Tworzenie potoków i wykonać testy przy użyciu możliwości **debugowania** w kanwie potoku bez pisania jednego wiersza kodu.

![Możliwość debugowania na kanwie potoku](media/iterative-development-debugging/iterative-development-image1.png)

Wyświetlanie wyników przebiegów testu w oknie **Dane wyjściowe** kanwy potoku.

![Okno wyjściowe kanwy potoku](media/iterative-development-debugging/iterative-development-image2.png)

Po pomyślnym uruchomieniu testu dodaj więcej działań do potoku i kontynuuj debugowanie w sposób iteracyjny. Można również **anulować** przebieg testu, gdy jest w toku.

![Anulowanie przebiegu testowego](media/iterative-development-debugging/iterative-development-image3.png)

Podczas wykonywania testów nie trzeba publikować zmian w fabryce danych przed wybraniem **opcji Debugowanie**. Ta funkcja jest przydatna w scenariuszach, w których chcesz upewnić się, że zmiany działają zgodnie z oczekiwaniami przed zaktualizowaniem przepływu pracy fabryki danych.

> [!IMPORTANT]
> Wybranie **debug** faktycznie uruchamia potoku. Tak na przykład, jeśli potok zawiera działanie kopiowania, przebieg testu kopiuje dane ze źródła do miejsca docelowego. W związku z tym zaleca się użycie folderów testowych w działaniach kopiowania i innych działań podczas debugowania. Po debugowaniu potoku przełącz się do rzeczywistych folderów, których chcesz użyć w normalnych operacjach.

## <a name="visualizing-debug-runs"></a>Wizualizacja przebiegów debugowania

Można wizualizować wszystkie przebiegi debugowania, które są w toku dla fabryki danych w jednym miejscu. Wybierz **Opcję Widok debugowanie jest uruchamiane** w prawym górnym rogu strony. Ta funkcja jest przydatna w scenariuszach, w których masz potoki główne rozpoczynające uruchamianie debugowania dla potoków podrzędnych i chcesz, aby jeden widok był widoczny wszystkie aktywne uruchomienia debugowania.

![Wybierz ikonę Wyświetl aktywne przebiegi debugowania](media/iterative-development-debugging/view-debug-runs-image1.png)

![Przykładowa lista aktywnych uruchomień debugowania](media/iterative-development-debugging/view-debug-runs-image2.png)

Jeśli masz aktywne sesje debugowania przepływu danych, te sesje pojawią się w dolnej części aktywnego okna debugowania. Można wybrać aktywną sesję przepływu danych i zatrzymać odpowiedni klaster.

![Przykładowa lista aktywnych przebiegów debugowania przepływu danych](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorowanie przebiegów debugowania

Przebiegi testowe zainicjowane z możliwością **debugowania** nie są dostępne na liście na karcie **Monitor.** Na karcie **Monitor** można wyświetlić tylko uruchomienia wyzwalane za pomocą **wyzwalaczy teraz,** **harmonogramu**lub okien **tumbling.** Można zobaczyć ostatnie uruchomienie testowe zainicjowane z możliwością **debugowania** w oknie **Dane wyjściowe** kanwy potoku.

## <a name="setting-breakpoints-for-debugging"></a>Ustawianie punktów przerwania do debugowania

Usługa Data Factory umożliwia również debugowanie, dopóki nie osiągniesz określonego działania na kanwie potoku. Wystarczy umieścić punkt przerwania na działanie, aż chcesz przetestować, i wybierz **debugowanie**. Fabryka danych zapewnia, że test jest uruchamiany tylko do momentu działania punktu przerwania na kanwie potoku. Ta funkcja *debugowania, dopóki* jest przydatna, gdy nie chcesz testować całego potoku, ale tylko podzbiór działań wewnątrz potoku.

![Punkty przerwania na kanwie potoku](media/iterative-development-debugging/iterative-development-image4.png)

Aby ustawić punkt przerwania, wybierz element na kanwie potoku. Opcja *Debugowania do* pojawia się jako puste czerwone kółko w prawym górnym rogu elementu.

![Przed ustawieniem punktu przerwania dla zaznaczonego elementu](media/iterative-development-debugging/iterative-development-image5.png)

Po wybraniu opcji *Debugowanie do* zmienia się ona w wypełnione czerwone kółko, aby wskazać, że punkt przerwania jest włączony.

![Po ustawieniu punktu przerwania na wybranym elemencie](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Następne kroki
[Ciągła integracja i wdrażanie w fabryce danych platformy Azure](continuous-integration-deployment.md)
