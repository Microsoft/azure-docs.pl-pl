---
title: Najlepsze rozwiązania dotyczące interfejsu API narzędzia do wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących wykrywania anomalii przy użyciu interfejsu API wykrywania anomalii.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: mbullwin
ms.openlocfilehash: 9457c610b256dd4602ef0dc51a47eeffb3c63b49
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705153"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Najlepsze rozwiązania dotyczące korzystania z interfejsu API wykrywania anomalii

Interfejs API wykrywania anomalii jest usługą bezstanową. Na dokładność i wydajność wyników może mieć wpływ:

* Jak są przygotowywane dane szeregów czasowych.
* Użyte parametry interfejsu API wykrywania anomalii.
* Liczba punktów danych w żądaniu interfejsu API. 

Skorzystaj z tego artykułu, aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących korzystania z interfejsu API w celu uzyskania najlepszych wyników dla danych. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Kiedy należy używać wykrywania anomalii w usłudze Batch (cała) lub Najnowsza (Ostatnia)

Punkt końcowy wykrywania wsadowego interfejsu API usługi wykrywanie anomalii pozwala wykrywać anomalie za pomocą całych danych szeregów czasowych. W tym trybie wykrywania tworzony jest pojedynczy model statystyczny, który jest stosowany do każdego punktu w zestawie danych. Jeśli Twoje serie czasowe mają poniższe cechy, zalecamy używanie wykrywania wsadowego do wyświetlania podglądu danych w jednym wywołaniu interfejsu API.

* Sezonowa seria czasowa z sporadycznymi anomaliami.
* Płaska seria czasu trendu, z sporadycznymi skokami/dipmi. 

Nie zalecamy korzystania z wykrywania anomalii usługi Batch do monitorowania danych w czasie rzeczywistym lub korzystania z nich w danych szeregów czasowych, które nie mają powyższych cech. 

* Wykrywanie partii tworzy i stosuje tylko jeden model, wykrywanie poszczególnych punktów odbywa się w kontekście całej serii. Jeśli dane szeregów czasowych są tendencją w górę i w dół bez sezonowości, niektóre punkty zmiany (DIP i skoki w danych) mogą zostać pominięte przez model. Podobnie niektóre punkty zmian, które są mniej znaczące niż później w zestawie danych, mogą nie być zliczane jako wystarczająco znaczące do włączenia do modelu.

* Wykrywanie partii jest wolniejsze niż wykrywanie stanu anomalii najnowszego punktu podczas monitorowania danych w czasie rzeczywistym ze względu na liczbę analizowanych punktów.

W przypadku monitorowania danych w czasie rzeczywistym zalecamy wykrycie stanu anomalii tylko dla najnowszego punktu danych. Nieustannie stosując funkcję wykrywania najnowszych punktów, monitorowanie danych przesyłanych strumieniowo może być wydajniejsze i dokładne.

W poniższym przykładzie opisano wpływ tych trybów wykrywania na wydajność. Pierwszy obraz przedstawia wynik ciągłego wykrywania stanu anomalii w najnowszym punkcie na 28 wcześniej obserwowanych punktów danych. Czerwone punkty to anomalie.

![Obraz przedstawiający wykrywanie anomalii przy użyciu najnowszego punktu](../media/last.png)

Poniżej znajduje się ten sam zestaw danych, który korzysta z wykrywania anomalii usługi Batch. Model skompilowany dla operacji zignorował kilka anomalii oznaczonych przez prostokąty.

![Obraz przedstawiający wykrywanie anomalii przy użyciu metody wsadowej](../media/entire.png)

## <a name="data-preparation"></a>Przygotowywanie danych

Interfejs API wykrywania anomalii akceptuje dane szeregów czasowych sformatowane w obiekcie żądania JSON. Szeregi czasowe mogą być wszelkimi danymi liczbowymi zarejestrowanymi w czasie w kolejności sekwencyjnej. Możesz wysyłać okna danych szeregów czasowych do punktu końcowego interfejsu API wykrywania anomalii, aby zwiększyć wydajność interfejsu API. Minimalna liczba punktów danych, które można wysłać wynosi 12, a maksymalna to 8640 punktów. [Stopień szczegółowości](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) jest definiowany jako częstotliwość próbkowania danych. 

Punkty danych wysyłane do interfejsu API wykrywania anomalii muszą mieć prawidłową sygnaturę czasową skoordynowanego czasu uniwersalnego (UTC) i wartość numeryczną. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Jeśli dane są próbkowane w niestandardowym przedziale czasu, można je określić przez dodanie `customInterval` atrybutu do żądania. Na przykład jeśli Twoja seria jest próbkowana co 5 minut, można dodać następujące dane do żądania JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Brakujące punkty danych

Brakujące punkty danych są wspólne w równomiernie dystrybuowanych zestawach danych szeregów czasowych, w szczególności z dokładnością (mały interwał próbkowania). Przykładowo dane próbkowane co kilka minut). Brak mniej niż 10% oczekiwanej liczby punktów w danych nie powinien mieć negatywnego wpływu na wyniki wykrywania. Rozważ wypełnienie przerw w danych na podstawie jego właściwości, takich jak podstawiane punkty danych z wcześniejszego okresu, interpolacji liniowej lub średniej przenoszonej.

### <a name="aggregate-distributed-data"></a>Agregowanie danych rozproszonych

Interfejs API wykrywania anomalii działa najlepiej na równomiernie rozmieszczonych seriach czasowych. Jeśli dane są rozproszone losowo, należy je agregować przez jednostkę czasu, na przykład na minutę, co godzinę lub codziennie.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Wykrywanie anomalii danych za pomocą wzorców sezonowych

Jeśli wiesz, że dane szeregów czasowych mają wzorzec sezonowy (jeden, który występuje w regularnych odstępach czasu), możesz poprawić dokładność i czas odpowiedzi interfejsu API. 

Określenie, `period` Kiedy utworzysz żądanie JSON, może zmniejszyć opóźnienia wykrywania anomalii o maksymalnie 50%. `period`Jest to liczba całkowita, która określa przybliżoną liczbę punktów danych potrzebnych przez serię czasową do powtarzania wzorca. Na przykład szeregi czasowe z jednym punktem danych dziennie byłyby `period` tak `7` , a serie czasowe z jednym punktem na godzinę (z tym samym wzorcem tygodniowym) byłyby `period`  `7*24` . Jeśli nie masz pewności, jakie są wzorce danych, nie musisz określać tego parametru.

Aby uzyskać najlepsze wyniki, podaj 4 wartości `period` punktów danych i dodatkowe. Na przykład dane godzinowe z wzorcem tygodniowym zgodnie z powyższym opisem powinny dostarczyć 673 punktów danych w treści żądania ( `7 * 24 * 4 + 1` ).

### <a name="sampling-data-for-real-time-monitoring"></a>Próbkowanie danych do monitorowania w czasie rzeczywistym

Jeśli dane przesyłane strumieniowo są próbkowane w krótkim odstępie czasu (na przykład w sekundach lub minutach), wysłanie zalecanej liczby punktów danych może przekroczyć maksymalną dozwoloną liczbę (8640 punktów danych). Jeśli dane pokazują stabilny wzorzec sezonowy, należy rozważyć wysłanie przykładu danych szeregów czasowych w dłuższym przedziale czasu, na przykład godzin. Próbkowanie danych w ten sposób może również znacznie poprawić czas odpowiedzi interfejsu API. 

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API narzędzia do wykrywania anomalii?](../overview.md)
* [Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu detektora anomalii](../quickstarts/client-libraries.md)