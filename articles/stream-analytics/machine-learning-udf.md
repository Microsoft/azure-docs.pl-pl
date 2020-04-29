---
title: Integracja Azure Stream Analytics z usługą Azure Machine Learning
description: W tym artykule opisano sposób integrowania zadania Azure Stream Analytics z modelami Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481970"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integracja Azure Stream Analytics z Azure Machine Learning (wersja zapoznawcza)

Modele uczenia maszynowego można zaimplementować jako funkcję zdefiniowaną przez użytkownika (UDF) w zadaniach Azure Stream Analytics, aby wykonywać oceny w czasie rzeczywistym i przewidywania dotyczące danych wejściowych przesyłania strumieniowego. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) umożliwia korzystanie z dowolnego popularnego narzędzia typu "open source", takiego jak Tensorflow, scikit-uczenie się lub PyTorch, do przygotowywania, uczenia i wdrażania modeli.

> [!NOTE]
> Ta funkcja jest w publicznej wersji zapoznawczej. Możesz uzyskać dostęp do tej funkcji na Azure Portal tylko przy użyciu [linku w wersji zapoznawczej portalu Stream Analytics](https://aka.ms/asaportalpreview). Ta funkcja jest również dostępna w najnowszej wersji [narzędzi Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem modelu uczenia maszynowego do zadania Stream Analytics należy wykonać następujące czynności:

1. Użyj Azure Machine Learning [, aby wdrożyć model jako usługę sieci Web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Skrypt oceniania powinien zawierać [przykładowe dane wejściowe i wyjściowe](../machine-learning/how-to-deploy-and-where.md#example-entry-script) , które są używane przez Azure Machine Learning do generowania specyfikacji schematu. Stream Analytics używa schematu, aby zrozumieć podpis funkcji usługi sieci Web.

3. Upewnij się, że usługa sieci Web akceptuje i zwraca dane serializowane JSON.

4. Wdróż swój model w [usłudze Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) na potrzeby wdrożeń produkcyjnych o dużej skali. Jeśli usługa sieci Web nie może obsłużyć liczby żądań wysyłanych z zadania, wydajność zadania Stream Analytics zostanie obniżona, co wpływa na opóźnienia. Modele wdrożone na Azure Container Instances nie są obecnie obsługiwane, ale staną się dostępne w najbliższych miesiącach.

## <a name="add-a-machine-learning-model-to-your-job"></a>Dodawanie modelu uczenia maszynowego do zadania

Do zadania Stream Analytics można dodawać funkcje Azure Machine Learning bezpośrednio z Azure Portal.

1. Przejdź do zadania Stream Analytics w Azure Portal, a następnie wybierz pozycję **funkcje** w obszarze **topologia zadania**. Następnie wybierz pozycję **Azure ml Service** z menu rozwijanego **+ Dodaj** .

   ![Dodawanie usługi Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Wypełnij formularz **funkcji usługi Azure Machine Learning** przy użyciu następujących wartości właściwości:

   ![Konfigurowanie usługi Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

W poniższej tabeli opisano każdą właściwość funkcji usługi Azure ML w Stream Analytics.

|Właściwość|Opis|
|--------|-----------|
|Alias funkcji|Wprowadź nazwę, aby wywołać funkcję w zapytaniu.|
|Subskrypcja|Twoja subskrypcja platformy Azure.|
|Obszar roboczy usługi Azure ML|Azure Machine Learning obszar roboczy, który został użyty do wdrożenia modelu jako usługi sieci Web.|
|Wdrożenia|Usługa sieci Web obsługująca model.|
|Sygnatura funkcji|Podpis usługi sieci Web wywnioskowanej ze specyfikacji schematu interfejsu API. Jeśli nie można załadować podpisu, sprawdź, czy podano przykładowe dane wejściowe i wyjściowe w skrypcie oceniania, aby automatycznie wygenerować schemat.|
|Liczba żądań równoległych na partycję|Jest to zaawansowana konfiguracja umożliwiająca optymalizację przepływności o dużej skali. Ta liczba przedstawia współbieżne żądania wysyłane z każdej partycji zadania do usługi sieci Web. Zadania z sześcioma jednostkami przesyłania strumieniowego (SU) i niższą mają jedną partycję. Zadania z 12 usługami SUs mają dwie partycje, 18 usługi SUs mają trzy partycje i tak dalej.<br><br> Na przykład jeśli zadanie ma dwie partycje i ustawisz ten parametr na cztery, do usługi sieci Web będzie osiem współbieżnych żądań z zadania. W tej chwili w publicznej wersji zapoznawczej ta wartość jest domyślnie równa 20 i nie można jej zaktualizować.|
|Maksymalna liczba partii|Jest to zaawansowana konfiguracja do optymalizowania przepływności o dużej skali. Ta liczba przedstawia maksymalną liczbę zdarzeń, które należy wykonać w ramach pojedynczego żądania wysyłanego do usługi sieci Web.|

## <a name="supported-input-parameters"></a>Obsługiwane parametry wejściowe

Gdy zapytanie Stream Analytics wywoła Azure Machine Learning UDF, zadanie tworzy serializowane żądanie JSON do usługi sieci Web. Żądanie jest oparte na schemacie specyficznym dla modelu. Musisz podać przykładowe dane wejściowe i wyjściowe w skrypcie oceniania, aby [automatycznie wygenerować schemat](../machine-learning/how-to-deploy-and-where.md). Schemat umożliwia Stream Analytics konstruowania serializowanego żądania JSON dla dowolnego z obsługiwanych typów danych, takich jak numpy, Pandas i PySpark. Wiele zdarzeń wejściowych można wsadowo w pojedynczym żądaniu.

Poniższe Stream Analytics Query to przykład wywołania Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics obsługuje tylko przekazywanie jednego parametru dla funkcji Azure Machine Learning. Może być konieczne przygotowanie danych przed przekazaniem ich jako danych wejściowych do formatu UDF w usłudze Machine Learning.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Przekazywanie wielu parametrów wejściowych do formatu UDF

Najczęściej spotykane przykłady danych wejściowych modeli uczenia maszynowego to numpy tablice i dataframes. Można utworzyć tablicę przy użyciu formatu UDF języka JavaScript i utworzyć wieloserializowaną ramkę danych JSON przy `WITH` użyciu klauzuli.

### <a name="create-an-input-array"></a>Utwórz tablicę wejściową

Można utworzyć kod UDF języka JavaScript, który akceptuje *N* liczba danych wejściowych i tworzy tablicę, która może być używana jako dane wejściowe dla Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Po dodaniu UDF języka JavaScript do zadania można wywołać Azure Machine Learning UDF przy użyciu następującego zapytania:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Poniższy kod JSON jest przykładowym żądaniem:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Tworzenie ramki danych Pandas lub PySpark

Możesz użyć klauzuli, `WITH` aby utworzyć serializowaną ramkę JSON, która może być przekazana jako dane wejściowe do Azure Machine Learning UDF, jak pokazano poniżej.

Następujące zapytanie tworzy ramkę danych, zaznaczając niezbędne pola i używa ramki Dataframe jako dane wejściowe do Azure Machine Learning UDF.

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

Poniższy kod JSON jest przykładowym żądaniem z poprzedniego zapytania:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optymalizacja wydajności Azure Machine Learning UDF

Podczas wdrażania modelu w usłudze Azure Kubernetes Service można [profilować model, aby określić wykorzystanie zasobów](../machine-learning/how-to-deploy-and-where.md#profilemodel). Możesz również [włączyć usługi App Insights dla wdrożeń](../machine-learning/how-to-enable-app-insights.md) , aby zrozumieć stawki żądań, czasy odpowiedzi i wskaźniki niepowodzeń.

Jeśli masz scenariusz o dużej przepływności zdarzeń, może być konieczna zmiana następujących parametrów w Stream Analytics, aby osiągnąć optymalną wydajność z niską opóźnieniami:

1. Maksymalna liczba partii.
2. Liczba równoległych żądań na partycję.

### <a name="determine-the-right-batch-size"></a>Określanie odpowiedniego rozmiaru partii

Po wdrożeniu usługi sieci Web wysyłaj przykładowe żądanie o różne rozmiary partii, rozpoczynając od 50 i zwiększając je w kolejności setek. Na przykład 200, 500, 1000, 2000 i tak dalej. Należy zauważyć, że po pewnym rozmiarze wsadu opóźnienie odpowiedzi wzrasta. Po upływie tego czasu opóźnienie odpowiedzi powinno być maksymalną liczbą partii dla danego zadania.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Określ liczbę żądań równoległych na partycję

W optymalnym skalowaniu zadanie Stream Analytics powinno być w stanie wysyłać wiele żądań równoległych do usługi sieci Web i uzyskać odpowiedź w ciągu kilku milisekund. Opóźnienie odpowiedzi usługi sieci Web może bezpośrednio wpływać na opóźnienia i wydajność zadania Stream Analytics. Jeśli wywołanie z zadania do usługi sieci Web zajmuje dużo czasu, prawdopodobnie zobaczysz wzrost opóźnienia w znaku wodnym i zobaczysz wzrost liczby zaległych zdarzeń wejściowych.

Aby zapobiec takim opóźnieniu, należy się upewnić, że Zainicjowano obsługę klastra usługi Azure Kubernetes Service (AKS) z odpowiednią [liczbą węzłów i replik](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Ważne jest, aby usługa sieci Web była wysoce dostępna i zwracała Pomyślne odpowiedzi. Jeśli zadanie odbierze niedostępną odpowiedź usługi (503) z usługi sieci Web, będzie stale ponawiać próbę z powrotem z użyciem wykładniczej. Każda odpowiedź inna niż powodzenie (200) i Usługa niedostępna (503) spowodują, że zadanie przejdzie do stanu niepowodzenia.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Skalowanie zadania Stream Analytics za pomocą funkcji Azure Machine Learning Studio (klasycznej)](stream-analytics-scale-with-machine-learning-functions.md)

