---
title: Funkcje w wersji zapoznawczej Azure Stream Analytics
description: W tym artykule wymieniono funkcje Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589654"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej Azure Stream Analytics

Ten artykuł podsumowuje wszystkie funkcje obecnie dostępne w wersji zapoznawczej dla Azure Stream Analytics. Używanie funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Uwierzytelnianie w celu SQL Database danych wyjściowych z tożsamościami zarządzanymi (wersja zapoznawcza)

Azure Stream Analytics obsługuje [uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla Azure SQL Database ujścia danych wyjściowych. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak konieczność ponownego uwierzytelnienia ze względu na zmiany hasła. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Ocena wysokiej wydajności w czasie rzeczywistym z niestandardowymi modelami ML zarządzanymi przez Azure Machine Learning

Azure Stream Analytics obsługuje wysoką wydajność i ocenianie w czasie rzeczywistym, wykorzystując niestandardowe wstępnie nauczone modele Machine Learning zarządzane przez Azure Machine Learning i hostowane w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), korzystając z przepływu pracy, który nie wymaga pisania kodu. [Utwórz konto](https://aka.ms/asapreview1) w wersji zapoznawczej

## <a name="c-custom-de-serializers"></a>Niestandardowe deserializatory języka C#
Deweloperzy mogą wykorzystać możliwości Azure Stream Analytics, aby przetwarzać dane w protobuf, XML lub w dowolnym formacie niestandardowym. Można zaimplementować [niestandardowe deserializacji](custom-deserializer-examples.md) w języku C#, które mogą być następnie używane do deserializacji zdarzeń odebranych przez Azure Stream Analytics.

## <a name="extensibility-with-c-custom-code"></a>Rozszerzalność przy użyciu niestandardowego kodu w języku C#

Deweloperzy tworzący moduły Stream Analytics w chmurze lub na IoT Edge mogą zapisywać lub ponownie używać niestandardowych funkcji języka C# i wywoływać je bezpośrednio w zapytaniu za pomocą [funkcji zdefiniowanych przez użytkownika](stream-analytics-edge-csharp-udf-methods.md).

## <a name="debug-query-steps-in-visual-studio"></a>Kroki zapytania debugowania w programie Visual Studio

Możesz łatwo wyświetlić podgląd pośredniego zestawu wierszy na diagramie danych podczas przeprowadzania testów lokalnych w Azure Stream Analytics Narzędzia dla programu Visual Studio. 


## <a name="live-data-testing-in-visual-studio"></a>Testowanie danych na żywo w programie Visual Studio

Narzędzia Visual Studio Tools for Azure Stream Analytics rozszerzają funkcję testowania lokalnego, która umożliwia testowanie zapytań dotyczących strumieni zdarzeń na żywo ze źródeł w chmurze, takich jak centrum zdarzeń lub Centrum IoT. Dowiedz się, jak [testować dane na żywo lokalnie przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code Azure Stream Analytics

Azure Stream Analytics zadania mogą być tworzone w Visual Studio Code. Zapoznaj się z naszym [samouczkiem wprowadzenie do vs Code](./quick-create-visual-studio-code.md).

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testowanie lokalne z danymi dynamicznymi w Visual Studio Code

Możesz przetestować zapytania dotyczące danych na żywo na komputerze lokalnym przed przesłaniem zadania do platformy Azure. Każda iteracja testowa trwa średnio od dwóch do trzech sekund, co skutkuje bardzo wydajnym procesem tworzenia oprogramowania.

