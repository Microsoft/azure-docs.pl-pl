---
title: Funkcje w wersji zapoznawczej Azure Stream Analytics
description: W tym artykule wymieniono funkcje Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: 4179b06759802025f97bd32a355b788c96c9eddb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123324"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej Azure Stream Analytics

Ten artykuł podsumowuje wszystkie funkcje obecnie dostępne w wersji zapoznawczej dla Azure Stream Analytics. Używanie funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są dostępne w publicznej wersji zapoznawczej. Można korzystać z tych funkcji dzisiaj, ale nie używać ich w środowisku produkcyjnym.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Uwierzytelnianie w celu SQL Database danych wyjściowych z tożsamościami zarządzanymi

Azure Stream Analytics obsługuje [uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla Azure SQL Database ujścia danych wyjściowych. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak konieczność ponownego uwierzytelnienia ze względu na zmiany hasła. 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Ocena wysokiej wydajności w czasie rzeczywistym z niestandardowymi modelami ML zarządzanymi przez Azure Machine Learning

Azure Stream Analytics obsługuje wysoką wydajność i ocenianie w czasie rzeczywistym, wykorzystując niestandardowe wstępnie nauczone modele Machine Learning zarządzane przez Azure Machine Learning i hostowane w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), korzystając z przepływu pracy, który nie wymaga pisania kodu. [Utwórz konto](https://aka.ms/asapreview1) w wersji zapoznawczej

### <a name="c-custom-de-serializers"></a>Niestandardowe deserializatory języka C#
Deweloperzy mogą wykorzystać możliwości Azure Stream Analytics, aby przetwarzać dane w protobuf, XML lub w dowolnym formacie niestandardowym. Można zaimplementować [niestandardowe deserializacji](custom-deserializer-examples.md) w języku C#, które mogą być następnie używane do deserializacji zdarzeń odebranych przez Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozszerzalność przy użyciu niestandardowego kodu w języku C#

Deweloperzy tworzący moduły Stream Analytics w chmurze lub na IoT Edge mogą zapisywać lub ponownie używać niestandardowych funkcji języka C# i wywoływać je bezpośrednio w zapytaniu za pomocą [funkcji zdefiniowanych przez użytkownika](stream-analytics-edge-csharp-udf-methods.md).

### <a name="debug-query-steps-in-visual-studio"></a>Kroki zapytania debugowania w programie Visual Studio

Możesz łatwo wyświetlić podgląd pośredniego zestawu wierszy na diagramie danych podczas przeprowadzania testów lokalnych w Azure Stream Analytics Narzędzia dla programu Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Testowanie danych na żywo w programie Visual Studio

Narzędzia Visual Studio Tools for Azure Stream Analytics rozszerzają funkcję testowania lokalnego, która umożliwia testowanie zapytań dotyczących strumieni zdarzeń na żywo ze źródeł w chmurze, takich jak centrum zdarzeń lub Centrum IoT. Dowiedz się, jak [testować dane na żywo lokalnie przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code Azure Stream Analytics

Azure Stream Analytics zadania mogą być tworzone w Visual Studio Code. Zapoznaj się z naszym [samouczkiem wprowadzenie do vs Code](./quick-create-visual-studio-code.md).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testowanie lokalne z danymi dynamicznymi w Visual Studio Code

Możesz przetestować zapytania dotyczące danych na żywo na komputerze lokalnym przed przesłaniem zadania do platformy Azure. Każda iteracja testowa trwa średnio od dwóch do trzech sekund, co skutkuje bardzo wydajnym procesem tworzenia oprogramowania.

## <a name="other-previews"></a>Inne wersje zapoznawcze

Poniższe funkcje są również dostępne w wersji zapoznawczej na żądanie.

### <a name="support-for-azure-stack"></a>Obsługa Azure Stack
Ta funkcja jest włączona w środowisku uruchomieniowym Azure IoT Edge, wykorzystuje niestandardowe funkcje Azure Stack, takie jak Natywna obsługa lokalnych wejść i wyjść w Azure Stack (na przykład Event Hubs, IoT Hub, Blob Storage). Ta nowa Integracja pozwala tworzyć architektury hybrydowe, które mogą analizować dane blisko miejsca, w którym są generowane, obniżać czas oczekiwania i maksymalizując szczegółowe informacje.
Ta funkcja umożliwia tworzenie architektur hybrydowych, które mogą analizować dane blisko miejsca, w którym są generowane, obniżające czas oczekiwania i maksymalizując szczegółowe informacje. Musisz [zarejestrować się](https://aka.ms/asapreview1) w tej wersji zapoznawczej.