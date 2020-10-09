---
title: Jak używać interfejsu API wykrywania anomalii w danych szeregów czasowych
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wykrywać anomalie w danych jako partia lub dane przesyłane strumieniowo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71840224"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Instrukcje: korzystanie z interfejsu API wykrywania anomalii w danych szeregów czasowych  

[Interfejs API wykrywania anomalii](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) oferuje dwie metody wykrycia anomalii. Możesz wykryć anomalie jako partię w szeregu czasowym lub w miarę generowania danych przez wykrycie stanu anomalii najnowszego punktu danych. Model wykrywania zwraca wyniki anomalii wraz z oczekiwaną wartością każdego punktu danych i górną i dolną granicą wykrywania anomalii. tych wartości można użyć do wizualizacji zakresu normalnych wartości i anomalii w danych.

## <a name="anomaly-detection-modes"></a>Tryby wykrywania anomalii 

Interfejs API wykrywania anomalii zapewnia tryby wykrywania: Batch i streaming.

> [!NOTE]
> Następujące adresy URL żądania muszą być połączone z odpowiednim punktem końcowym dla subskrypcji. Na przykład: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Wykrywanie partii

Aby wykryć anomalie w ramach partii punktów danych w danym przedziale czasu, użyj następującego identyfikatora URI żądania z danymi szeregów czasowych: 

`/timeseries/entire/detect`. 

Wysyłając dane szeregów czasowych jednocześnie, interfejs API będzie generować model przy użyciu całej serii i analizować każdy punkt danych.  

### <a name="streaming-detection"></a>Wykrywanie przesyłania strumieniowego

Aby ciągle wykrywać anomalie na danych przesyłanych strumieniowo, użyj następującego identyfikatora URI żądania z najnowszym punktem danych: 

`/timeseries/last/detect'`. 

Wysyłając nowe punkty danych podczas ich generowania, można monitorować dane w czasie rzeczywistym. Model zostanie wygenerowany przy użyciu wysyłanych punktów danych, a interfejs API określi, czy najnowszy punkt w szeregu czasowym jest anomalią.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Dopasowywanie dolnych i górnych granic wykrywania anomalii

Domyślnie górne i dolne granice wykrywania anomalii są obliczane przy użyciu `expectedValue` , `upperMargin` i `lowerMargin` . Jeśli potrzebujesz różnych granic, zalecamy zastosowanie `marginScale` do `upperMargin` lub `lowerMargin` . Granice zostałyby obliczone w następujący sposób:

|Obwiedni  |Obliczenia  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

W poniższych przykładach pokazano wynik interfejsu API wykrywania anomalii w różnych sensitivities.

### <a name="example-with-sensitivity-at-99"></a>Przykład o czułości o 99

![Domyślna czułość](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Przykład o czułości o 95

![czułość 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Przykład o czułości o 85

![czułość 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API narzędzia do wykrywania anomalii?](../overview.md)
* [Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii](../quickstarts/detect-data-anomalies-csharp.md)
