---
title: Integracja usługi Azure Stream Analytics z usługą Azure Machine Learning
description: W tym artykule opisano sposób integrowania zadania usługi Azure Stream Analytics z modelami usługi Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481970"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integracja usługi Azure Stream Analytics z usługą Azure Machine Learning (wersja zapoznawcza)

Można zaimplementować modele uczenia maszynowego jako funkcję zdefiniowaną przez użytkownika (UDF) w zadaniach usługi Azure Stream Analytics, aby wykonywać ocenianie w czasie rzeczywistym i przewidywanie danych wejściowych przesyłania strumieniowego. [Usługa Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) umożliwia używanie dowolnego popularnego narzędzia typu open source, takiego jak Tensorflow, scikit-learn lub PyTorch, do przygotowywania, uczenia i wdrażania modeli.

> [!NOTE]
> Ta funkcja jest w publicznej wersji zapoznawczej. Dostęp do tej funkcji można uzyskać w witrynie Azure portal tylko za pomocą [łącza w wersji zapoznawczej portalu usługi Stream Analytics](https://aka.ms/asaportalpreview). Ta funkcja jest również dostępna w najnowszej wersji [narzędzi usługi Stream Analytics dla programu Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj następujące kroki przed dodaniem modelu uczenia maszynowego jako funkcji do zadania usługi Stream Analytics:

1. Użyj usługi Azure Machine Learning, aby [wdrożyć model jako usługę sieci web.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. Skrypt oceniania powinien mieć [przykładowe dane wejściowe i wyjściowe,](../machine-learning/how-to-deploy-and-where.md#example-entry-script) które są używane przez usługę Azure Machine Learning do generowania specyfikacji schematu. Usługa Stream Analytics używa schematu do zrozumienia podpisu funkcji usługi sieci web.

3. Upewnij się, że usługa sieci web akceptuje i zwraca dane seryjne JSON.

4. Wdrażanie modelu w [usłudze Azure Kubernetes dla](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) wdrożeń produkcyjnych na dużą skalę. Jeśli usługa sieci web nie jest w stanie obsłużyć liczby żądań pochodzących z zadania, wydajność zadania usługi Stream Analytics zostanie obniżona, co ma wpływ na opóźnienie. Modele wdrożone w przypadku wystąpienia kontenerów platformy Azure nie są obsługiwane dzisiaj, ale staną się dostępne w nadchodzących miesiącach.

## <a name="add-a-machine-learning-model-to-your-job"></a>Dodawanie modelu uczenia maszynowego do zadania

Funkcje usługi Azure Machine Learning można dodać do zadania usługi Stream Analytics bezpośrednio z witryny Azure portal.

1. Przejdź do zadania usługi Stream Analytics w witrynie Azure portal i wybierz pozycję **Funkcje** w obszarze **Topologia zadań**. Następnie wybierz **usługę Azure ML z** menu rozwijanego + **Dodaj.**

   ![Dodawanie usługi Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Wypełnij formularz **funkcji usługi Azure Machine Learning Service** następującymi wartościami właściwości:

   ![Konfigurowanie usługi Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

W poniższej tabeli opisano każdą właściwość funkcji usługi Azure ML w usłudze Stream Analytics.

|Właściwość|Opis|
|--------|-----------|
|Alias funkcji|Wprowadź nazwę, aby wywołać funkcję w kwerendzie.|
|Subskrypcja|Twoja subskrypcja platformy Azure..|
|Obszar roboczy usługi Azure ML|Obszar roboczy usługi Azure Machine Learning używany do wdrażania modelu jako usługi sieci web.|
|Wdrożenia|Usługa sieci web obsługujących model.|
|Podpis funkcji|Podpis usługi sieci web wywnioskować ze specyfikacji schematu interfejsu API. Jeśli podpis nie powiedzie się załadować, sprawdź, czy podano przykładowe dane wejściowe i wyjściowe w skrypcie oceniania, aby automatycznie wygenerować schemat.|
|Liczba równoległych żądań na partycję|Jest to zaawansowana konfiguracja w celu optymalizacji przepływności na dużą skalę. Ten numer reprezentuje równoczesnych żądań wysyłanych z każdej partycji zadania do usługi sieci web. Zadania z sześcioma jednostkami przesyłania strumieniowego (SU) i niższymi mają jedną partycję. Zadania z 12 SU mają dwie partycje, 18 SUs mają trzy partycje i tak dalej.<br><br> Na przykład jeśli zadanie ma dwie partycje i ustawisz ten parametr na cztery, będzie osiem równoczesnych żądań z zadania do usługi sieci web. W tej chwili publicznej wersji zapoznawczej tej wartości domyślnie 20 i nie można zaktualizować.|
|Maksymalna liczba partii|Jest to zaawansowana konfiguracja do optymalizacji przepływności na dużą skalę. Ta liczba reprezentuje maksymalną liczbę zdarzeń, które mają być wsadowe razem w jednym żądaniu wysłanym do usługi sieci web.|

## <a name="supported-input-parameters"></a>Obsługiwane parametry wejściowe

Gdy zapytanie usługi Stream Analytics wywołuje udf usługi Azure Machine Learning, zadanie tworzy żądanie serializowane JSON do usługi sieci web. Żądanie jest oparte na schemacie specyficznym dla modelu. Aby [automatycznie wygenerować schemat,](../machine-learning/how-to-deploy-and-where.md)należy podać przykładowe dane wejściowe i wyjściowe w skrypcie oceniania. Schemat umożliwia usługi Stream Analytics do konstruowania żądań serializowanych JSON dla dowolnego z obsługiwanych typów danych, takich jak numpy, pandas i PySpark. Wiele zdarzeń wejściowych mogą być wsadowe razem w jednym żądaniu.

Następujące zapytanie usługi Stream Analytics jest przykładem sposobu wywoływania udf usługi Azure Machine Learning:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Usługa Stream Analytics obsługuje tylko jeden parametr dla funkcji usługi Azure Machine Learning. Może być konieczne przygotowanie danych przed przekazaniem ich jako danych wejściowych do udf uczenia maszynowego.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Przekazywanie wielu parametrów wejściowych do UDF

Najczęstsze przykłady danych wejściowych do modeli uczenia maszynowego są numpy tablice i DataFrames. Można utworzyć tablicę przy użyciu języka JavaScript UDF i utworzyć moduł `WITH` DataFrame z serialem JSON przy użyciu klauzuli.

### <a name="create-an-input-array"></a>Tworzenie tablicy wejściowej

Można utworzyć UDF JavaScript, który akceptuje *N* liczba wejść i tworzy tablicę, która może służyć jako dane wejściowe do usługi Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Po dodaniu interfejsu UDF javascript do zadania można wywołać udf usługi Azure Machine Learning przy użyciu następującej kwerendy:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Następujące JSON jest przykładowe żądanie:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Tworzenie pandas lub PySpark DataFrame

Można użyć `WITH` klauzuli do utworzenia JSON serializowane DataFrame, które mogą być przekazywane jako dane wejściowe do usługi Azure Machine Learning UDF, jak pokazano poniżej.

Następująca kwerenda tworzy DataFrame, wybierając niezbędne pola i używa DataFrame jako dane wejściowe do usługi Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

Następujący JSON jest przykładowe żądanie z poprzedniej kwerendy:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optymalizacja wydajności dla plików UEDF usługi Azure Machine Learning

Podczas wdrażania modelu w usłudze Azure Kubernetes można profilować model w [celu określenia wykorzystania zasobów.](../machine-learning/how-to-deploy-and-where.md#profilemodel) Można również [włączyć usługę App Insights dla wdrożeń,](../machine-learning/how-to-enable-app-insights.md) aby zrozumieć szybkość żądań, czas odpowiedzi i współczynniki niepowodzeń.

Jeśli masz scenariusz o wysokiej przepływności zdarzeń, może być konieczna zmiana następujących parametrów w usłudze Stream Analytics, aby osiągnąć optymalną wydajność przy niskich opóźnieniach typu end-to-end:

1. Maksymalna liczba partii.
2. Liczba równoległych żądań na partycję.

### <a name="determine-the-right-batch-size"></a>Określanie odpowiedniego rozmiaru partii

Po wdrożeniu usługi sieci web należy wysłać przykładowe żądanie o różnych rozmiarach partii, począwszy od 50 i zwiększając je w kolejności setek. Na przykład 200, 500, 1000, 2000 i tak dalej. Można zauważyć, że po pewnym rozmiarze partii zwiększa się opóźnienie odpowiedzi. Punkt, po którym opóźnienie zwiększa odpowiedź powinna być maksymalna liczba partii dla zadania.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Określanie liczby równoległych żądań na partycję

Przy optymalnym skalowaniu zadanie usługi Stream Analytics powinno mieć możliwość wysyłania wielu równoległych żądań do usługi sieci web i uzyskania odpowiedzi w ciągu kilku milisekund. Opóźnienie odpowiedzi usługi sieci web może bezpośrednio wpłynąć na opóźnienie i wydajność zadania usługi Stream Analytics. Jeśli wywołanie z zadania do usługi sieci web zajmuje dużo czasu, prawdopodobnie zobaczysz wzrost opóźnienia znaku wodnego i może również zobaczyć wzrost liczby zaległych zdarzeń wejściowych.

Aby zapobiec takim opóźnieniom, upewnij się, że klaster usługi Azure Kubernetes Service (AKS) został aprowizny z [odpowiednią liczbą węzłów i replik](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Bardzo ważne jest, aby usługa sieci web była wysoce dostępna i zwracała pomyślne odpowiedzi. Jeśli zadanie otrzyma odpowiedź niedostępną dla usługi (503) z usługi sieci web, będzie ona stale ponawiać próbę z wykładniczym wycofywaniem. Każda odpowiedź inna niż sukces (200) i usługa niedostępna (503) spowoduje, że zadanie przejdzie do stanu awarii.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Skalowanie zadania usługi Stream Analytics za pomocą funkcji usługi Azure Machine Learning Studio (klasycznej)](stream-analytics-scale-with-machine-learning-functions.md)

