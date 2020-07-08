---
title: Funkcje w wersji zapoznawczej Azure Stream Analytics
description: W tym artykule wymieniono funkcje Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7391fbccaf7983a070d80da64a2908333280420b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83609005"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej Azure Stream Analytics

Ten artykuł podsumowuje wszystkie funkcje obecnie dostępne w wersji zapoznawczej dla Azure Stream Analytics. Używanie funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są dostępne w publicznej wersji zapoznawczej. Można korzystać z tych funkcji dzisiaj, ale nie używać ich w środowisku produkcyjnym.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Uwierzytelnianie w celu SQL Database danych wyjściowych z tożsamościami zarządzanymi

Azure Stream Analytics obsługuje [uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla Azure SQL Database ujścia danych wyjściowych. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak potrzeba ponownego uwierzytelnienia ze względu na zmiany hasła lub wygaśnięcia tokenów użytkowników, które wystąpiły co 90 dni. Po usunięciu potrzeby ręcznego uwierzytelniania Stream Analytics wdrożenia mogą być w pełni zautomatyzowane.

### <a name="output-to-azure-synapse-analytics"></a>Dane wyjściowe do usługi Azure Synapse Analytics

Azure Stream Analytics zadania mogą wynikać z tabeli puli SQL w [usłudze Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) i mogą przetwarzać stawki przepływności do 200 MB/s. Obsługuje to najbardziej wymagające analiz w czasie rzeczywistym i wymaga przetwarzania danych na gorąco w przypadku obciążeń, takich jak raportowanie i pulpit nawigacyjny.  


### <a name="online-scaling"></a>Skalowanie online

Skalowanie online nie wymaga zatrzymania zadania, jeśli trzeba zmienić alokację SU. Można zwiększyć lub zmniejszyć pojemność SU uruchomionego zadania bez konieczności jego zatrzymania. Ta kompilacja dotyczy obietnicy klientów długotrwałych potoków o krytycznym znaczeniu, które Stream Analytics obecnie oferowane. Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure Stream Analytics przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Niestandardowe deserializatory języka C#
Deweloperzy mogą wykorzystać możliwości Azure Stream Analytics, aby przetwarzać dane w protobuf, XML lub w dowolnym formacie niestandardowym. Można zaimplementować [niestandardowe deserializacji](custom-deserializer-examples.md) w języku C#, które mogą być następnie używane do deserializacji zdarzeń odebranych przez Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozszerzalność przy użyciu niestandardowego kodu w języku C#

Deweloperzy tworzący moduły Stream Analytics w chmurze lub na IoT Edge mogą zapisywać lub ponownie używać niestandardowych funkcji języka C# i wywoływać je bezpośrednio w zapytaniu za pomocą [funkcji zdefiniowanych przez użytkownika](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Kroki zapytania debugowania w programie Visual Studio

Możesz łatwo wyświetlić podgląd pośredniego zestawu wierszy na diagramie danych podczas przeprowadzania testów lokalnych w Azure Stream Analytics Narzędzia dla programu Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testowanie lokalne z danymi dynamicznymi w Visual Studio Code

Możesz przetestować zapytania dotyczące danych na żywo na komputerze lokalnym przed przesłaniem zadania do platformy Azure. Każda iteracja testowa trwa średnio od dwóch do trzech sekund, co skutkuje bardzo wydajnym procesem tworzenia oprogramowania.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code Azure Stream Analytics

Azure Stream Analytics zadania mogą być tworzone w Visual Studio Code. Zapoznaj się z naszym [samouczkiem wprowadzenie do vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Ocena wysokiej wydajności w czasie rzeczywistym z niestandardowymi modelami ML zarządzanymi przez Azure Machine Learning

Azure Stream Analytics obsługuje wysoką wydajność i ocenianie w czasie rzeczywistym, wykorzystując niestandardowe wstępnie nauczone modele Machine Learning zarządzane przez Azure Machine Learning i hostowane w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), korzystając z przepływu pracy, który nie wymaga pisania kodu. [Utwórz konto](https://aka.ms/asapreview1) w wersji zapoznawczej


### <a name="live-data-testing-in-visual-studio"></a>Testowanie danych na żywo w programie Visual Studio

Narzędzia Visual Studio Tools for Azure Stream Analytics rozszerzają funkcję testowania lokalnego, która umożliwia testowanie zapytań dotyczących strumieni zdarzeń na żywo ze źródeł w chmurze, takich jak centrum zdarzeń lub Centrum IoT. Dowiedz się, jak [testować dane na żywo lokalnie przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funkcje zdefiniowane przez użytkownika platformy .NET na IoT Edge

Za pomocą standardowych funkcji zdefiniowanych przez użytkownika w programie .NET można uruchamiać .NET Standard kodu w ramach potoku przesyłania strumieniowego. Możesz utworzyć proste klasy języka C# lub zaimportować pełny projekt i biblioteki. W programie Visual Studio jest obsługiwane pełne środowisko tworzenia i debugowania. Aby uzyskać więcej informacji, odwiedź stronę [opracowywanie .NET standard funkcji zdefiniowanych przez użytkownika dla Azure Stream Analytics zadań brzegowych](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Inne wersje zapoznawcze

Poniższe funkcje są również dostępne w wersji zapoznawczej na żądanie.

### <a name="support-for-azure-stack"></a>Obsługa Azure Stack
Ta funkcja jest włączona w środowisku uruchomieniowym Azure IoT Edge, wykorzystuje niestandardowe funkcje Azure Stack, takie jak Natywna obsługa lokalnych wejść i wyjść w Azure Stack (na przykład Event Hubs, IoT Hub, Blob Storage). Ta nowa Integracja pozwala tworzyć architektury hybrydowe, które mogą analizować dane blisko miejsca, w którym są generowane, obniżać czas oczekiwania i maksymalizując szczegółowe informacje.
Ta funkcja umożliwia tworzenie architektur hybrydowych, które mogą analizować dane blisko miejsca, w którym są generowane, obniżające czas oczekiwania i maksymalizując szczegółowe informacje. Musisz [zarejestrować się](https://aka.ms/asapreview1) w tej wersji zapoznawczej.
