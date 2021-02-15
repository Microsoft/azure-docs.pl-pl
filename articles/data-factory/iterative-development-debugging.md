---
title: Iteracyjne programowanie i debugowanie w Azure Data Factory
description: Dowiedz się, jak zaprojektować i debugować potoki Data Factory w interfejsie użytkownika ADF
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 90f3f57fa527c8aaeb32a7dcf41f461ff5f0bf77
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392531"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Debugowanie i programowanie przyrostowe za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory umożliwia iteracyjne opracowywanie i debugowanie potoków Data Factory podczas opracowywania rozwiązań integracji danych. Te funkcje umożliwiają testowanie zmian przed utworzeniem żądania ściągnięcia lub publikowaniem ich w usłudze Data Factory. 

Aby uzyskać 8-minutowy wprowadzenie i demonstrację tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Debugowanie potoku

Podczas tworzenia przy użyciu kanwy potoku można testować działania przy użyciu możliwości **debugowania** . Po uruchomieniu testów nie trzeba publikować zmian w fabryce danych przed wybraniem opcji **Debuguj**. Ta funkcja jest przydatna w scenariuszach, w których należy upewnić się, że zmiany działają zgodnie z oczekiwaniami przed aktualizacją przepływu pracy fabryki danych.

![Możliwość debugowania na kanwie potoku](media/iterative-development-debugging/iterative-development-1.png)

Gdy potok jest uruchomiony, wyniki poszczególnych działań można zobaczyć na karcie **dane wyjściowe** kanwy potoku.

Wyniki przebiegów testów są wyświetlane w oknie **dane wyjściowe** kanwy potoku.

![Okno dane wyjściowe kanwy potoku](media/iterative-development-debugging/iterative-development-2.png)

Po pomyślnym uruchomieniu testu Dodaj więcej działań do potoku i Kontynuuj debugowanie w sposób iteracyjny. Możesz również **anulować** przebieg testu, gdy jest w toku.

> [!IMPORTANT]
> Wybranie pozycji **Debuguj** powoduje rzeczywiste uruchomienie potoku. Na przykład, jeśli potok zawiera działanie kopiowania, przebieg testu kopiuje dane ze źródła do miejsca docelowego. W związku z tym zalecamy korzystanie z folderów testowych w działaniach kopiowania i innych działaniach podczas debugowania. Po debugowaniu potoku przejdź do folderów rzeczywistych, które mają być używane w normalnych operacjach.

### <a name="setting-breakpoints"></a>Ustawianie punktów przerwania

Azure Data Factory umożliwia debugowanie potoku do momentu osiągnięcia określonego działania na kanwie potoku. Umieść punkt przerwania dla działania, do którego chcesz przeprowadzić test, a następnie wybierz polecenie **Debuguj**. Data Factory zapewnia, że test będzie wykonywany tylko do działania punktu przerwania na kanwie potoku. Ten *Debuguj do momentu* , gdy nie chcesz testować całego potoku, ale tylko podzbiór działań w potoku.

![Punkty przerwania na kanwie potoku](media/iterative-development-debugging/iterative-development-3.png)

Aby ustawić punkt przerwania, wybierz element na kanwie potoku. Opcja *Debuguj do momentu* pojawi się jako puste czerwone koło w prawym górnym rogu elementu.

![Przed ustawieniem punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-4.png)

Po wybraniu opcji *Debuguj do momentu* zostanie ona zmieniona na wypełniony czerwony okrąg, aby wskazać, że punkt przerwania jest włączony.

![Po ustawieniu punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Monitorowanie przebiegów debugowania

Po uruchomieniu debugowania potoku wyniki pojawią się w oknie **danych wyjściowych** kanwy potoku. Karta dane wyjściowe będzie zawierać tylko ostatnie uruchomienie, które wystąpiło podczas bieżącej sesji przeglądarki. 

![Okno dane wyjściowe kanwy potoku](media/iterative-development-debugging/iterative-development-2.png)

Aby wyświetlić historyczny widok przebiegów debugowania lub zobaczyć listę wszystkich aktywnych przebiegów debugowania, możesz przejść do środowiska **monitorowania** . 

![Wybierz ikonę Wyświetl aktywne uruchomienia debugowania](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Usługa Azure Data Factory przechowuje tylko historię przebiegów debugowania przez 15 dni. 

## <a name="debugging-mapping-data-flows"></a>Debugowanie, mapowanie przepływów danych

Mapowanie przepływów danych umożliwia tworzenie logiki transformacji danych bez kodu, która jest uruchamiana na dużą skalę. Podczas kompilowania logiki można włączyć sesję debugowania, aby interaktywnie współpracować z danymi przy użyciu klastra Spark na żywo. Aby dowiedzieć się więcej, przeczytaj informacje o [mapowaniu trybu debugowania przepływu danych](concepts-data-flow-debug-mode.md).

Sesje debugowania aktywnych przepływów danych można monitorować za pośrednictwem fabryki w środowisku **monitorowania** .

![Wyświetl sesje debugowania przepływu danych](media/iterative-development-debugging/view-dataflow-debug-sessions.png)
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Debugowanie potoku za pomocą działania przepływu danych

Podczas wykonywania potoku debugowania z przepływem danych dostępne są dwie opcje obliczeń do użycia. Możesz użyć istniejącego klastra debugowania lub uruchomić nowy klaster just in Time dla przepływów danych.

Użycie istniejącej sesji debugowania znacznie skraca czas uruchamiania przepływu danych, ponieważ klaster jest już uruchomiony, ale nie jest zalecany w przypadku obciążeń złożonych ani równoległych, ponieważ może się to nie powieść, gdy wiele zadań jest uruchomionych jednocześnie.

Użycie środowiska uruchomieniowego działania spowoduje utworzenie nowego klastra przy użyciu ustawień określonych w ramach środowiska Integration Runtime każdego działania przepływu danych. Dzięki temu każde zadanie ma być izolowane i powinno być używane na potrzeby złożonych obciążeń lub testowania wydajności. Możesz również kontrolować czas wygaśnięcia w Azure IR tak, aby zasoby klastra używane na potrzeby debugowania nadal były dostępne dla tego okresu, aby obsłużyć dodatkowe żądania zadań.

> [!NOTE]
> Jeśli masz potok ze przepływem danych wykonywanym równolegle, wybierz opcję "Użyj środowiska uruchomieniowego działania", aby Data Factory mógł użyć Integration Runtime wybranego w działaniu przepływu danych. Pozwoli to na wykonywanie przepływów danych w wielu klastrach i może obsłużyć wykonywanie równoległych przepływów danych.

![Uruchamianie potoku z przepływu danych](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Następne kroki

Po przetestowaniu zmian Przekształć je w wyższe środowiska przy użyciu [ciągłej integracji i wdrażania w Azure Data Factory](continuous-integration-deployment.md).
